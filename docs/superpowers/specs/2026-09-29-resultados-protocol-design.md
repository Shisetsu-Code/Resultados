# Diseño: repositorio de resultados y protocolo ejecutable

Fecha: 2026-09-29

## Objetivo

`Shisetsu-Code/Resultados` será la fuente canónica de protocolos observados en juegos demo/test autorizados.

Debe servir simultáneamente para:

1. lectura humana;
2. clasificación técnica;
3. búsqueda rápida por proveedor, juego, acción o estado;
4. reconstrucción de requests reales;
5. validación posterior contra el servidor con una sesión viva;
6. trazabilidad histórica de lo observado;
7. evitar ejecutar acciones fuera del estado correcto del juego.

No debe ser sólo documentación. Debe poder actuar como catálogo ejecutable de protocolos.

## Principios

- Separar datos estructurados de explicación humana.
- Separar definición canónica de acción, modelo esperado de respuesta e historial real.
- Toda acción debe declarar sus precondiciones y relaciones con otras acciones/estados.
- Una acción con N opciones sólo puede considerarse completa con cobertura N/N.
- Si falta alguna opción, debe quedar explícitamente `INCOMPLETE`.
- Distinguir siempre:
  - `observed`: visto realmente en tráfico;
  - `inferred`: deducido pero no demostrado;
  - `validated`: reproducido posteriormente contra servidor y verificado.
- Secretos y campos de sesión nunca se almacenan como valores reutilizables.

## Estructura del repositorio

```text
Resultados/
├── README.md
├── schema/
│   ├── result.schema.json
│   ├── provider.schema.json
│   ├── response-model.schema.json
│   └── history.schema.json
└── <provider>/
    ├── README.md
    ├── provider.json
    └── <game-slug>/
        ├── README.md
        ├── index.json
        ├── result.json
        ├── responses/
        │   ├── <response-model>.json
        │   └── ...
        └── history/
            ├── <history-id>.json
            └── ...
```

## README raíz

Debe explicar:

- propósito del repo;
- estados de cobertura;
- diferencia entre `observed`, `inferred` y `validated`;
- cómo leer un juego;
- cómo usar una acción como request;
- significado de placeholders dinámicos;
- cómo se relacionan acciones y estados.

## Carpeta de proveedor

Cada proveedor tendrá:

### `README.md`

Resumen humano:

- juegos analizados;
- estado de cada juego;
- endpoints comunes;
- patrones compartidos;
- excepciones;
- limitaciones conocidas;
- notas operativas.

### `provider.json`

Índice estructurado del proveedor.

Debe contener:

```json
{
  "provider_id": "7mojos",
  "display_name": "7Mojos",
  "protocol_family": "horances",
  "games": [],
  "common_endpoints": [],
  "common_dynamic_fields": [],
  "common_states": [],
  "notes": []
}
```

No se promueve un comportamiento a nivel de proveedor hasta haberlo observado en más de un juego o haberlo validado explícitamente.

## Carpeta de juego

### `result.json`

Fuente canónica de acciones conocidas del juego.

Estructura conceptual:

```json
{
  "schema_version": 1,
  "provider_id": "7mojos",
  "game_id": "piggy-bank-bonanza",
  "title": "Piggy Bank Bonanza",
  "source_url": "https://www.7mojos.com/slots/piggy-bank-bonanza",
  "client": {
    "url": "https://de-cgm.horances.com/slots/2/",
    "game_token": "pbb"
  },
  "status": "PARTIAL",
  "actions": {}
}
```

## Definición de una acción

Cada acción debe tener un ID estable.

Ejemplo:

```json
{
  "action_id": "normal_spin",
  "classification": {
    "category": "SPIN",
    "natural_name": "Tirada normal",
    "description_es": "Realiza una tirada normal de 40 líneas."
  },
  "evidence_state": "observed",
  "relation": {
    "belongs_to": "base_game",
    "parent_action": null,
    "requires_state": "IDLE",
    "produces_states": [
      "ROUND_FINISHED",
      "WIN_PENDING_GAMBLE",
      "BONUS_RUNNING"
    ]
  },
  "preconditions": [],
  "wire": {
    "method": "POST",
    "url": "https://de-se.horances.com/api/v2/spin/placebet?1.15.2.2390",
    "headers": {
      "Content-Type": "application/json",
      "Authorization": "{{LIVE_AUTHORIZATION}}"
    },
    "body": {
      "linesCount": 40,
      "betPerLine": 0.03,
      "usingOperatorFreeSpins": false,
      "spinMode": null
    }
  },
  "copy_paste": {
    "curl": "..."
  },
  "response_model_id": "spin_normal_success_v1",
  "history_refs": [],
  "coverage": {
    "status": "COMPLETE"
  },
  "notes": []
}
```

## Lenguaje humano y lenguaje del juego

Cada acción debe mostrar ambos.

Ejemplo:

- natural: "Tirada normal de 40 líneas a 0.03 PLM por línea"
- wire: `linesCount=40`, `betPerLine=0.03`, `spinMode=null`

Para compras:

- natural: "Compra Free Spins por 100x la apuesta base"
- wire: `spinMode="BB100"`

Para elecciones:

- natural: "Apostar la ganancia al palo corazones"
- wire: `choiceType=1`, `option=<valor demostrado>`

## Requests copiables

Toda acción reproducible debe contener una request utilizable directamente.

Formato mínimo:

- método;
- URL completa;
- headers requeridos;
- body/query exacto;
- cURL copiable.

Sólo se permiten placeholders para valores dinámicos o secretos:

- `{{LIVE_AUTHORIZATION}}`
- `{{SESSION_ID}}`
- `{{COOKIE}}`
- `{{CRID}}`
- equivalentes específicos del proveedor.

Los campos estáticos observados deben quedar con su valor real.

## Relaciones y máquina de estados

El repositorio debe impedir conceptualmente tratar las acciones como independientes.

Ejemplo:

```text
IDLE
  |
  +-- normal_spin
        |
        +-- ROUND_FINISHED
        |
        +-- WIN_PENDING_GAMBLE
              |
              +-- gamble_color_red
              +-- gamble_color_black
              +-- gamble_suit_spade
              +-- gamble_suit_heart
              +-- gamble_suit_diamond
              +-- gamble_suit_club
              +-- collect
```

Una acción debe declarar:

- `requires_state`;
- `parent_action` cuando corresponda;
- grupo lógico mediante `belongs_to`;
- estados posibles de salida;
- precondiciones derivadas de la respuesta previa.

Ejemplo de precondición:

```json
{
  "path": "data.gamble.status",
  "contains": {
    "choiceType": 1
  }
}
```

Así un futuro validador puede rechazar una request de gamble si la respuesta previa no habilitó gamble.

## Elecciones múltiples

Regla estricta:

`N opciones visibles => N/N opciones demostradas`

Ejemplo:

```json
{
  "choice_domain": {
    "expected": 4,
    "tested": 4,
    "options": [
      {
        "label": "spade",
        "option": 0,
        "evidence_state": "observed"
      }
    ]
  }
}
```

Si no se completó:

```json
{
  "status": "INCOMPLETE",
  "tested": 1,
  "expected": 4,
  "untested": ["heart", "diamond", "club"]
}
```

Nunca se considera completo un dominio por haber probado una sola opción representativa.

## Modelos de respuesta

Cada acción referencia un archivo dentro de `responses/`.

Ejemplo:

```json
{
  "response_model_id": "spin_normal_success_v1",
  "action_id": "normal_spin",
  "expected": {
    "http_status": 200,
    "content_type": "application/json",
    "required": {
      "success": true,
      "data.bet.betTotal": "number_string",
      "data.bet.balanceInitial.cash": "number_string",
      "data.bet.balanceAfterStart.cash": "number_string",
      "data.steps": "array"
    }
  },
  "invariants": [
    "response.success == true",
    "response.data.bet.betTotal == requested_total_bet"
  ],
  "variable_fields": [
    "data.timestamp",
    "data.steps",
    "data.bet.totalEarns"
  ]
}
```

El objetivo no es comparar byte a byte una respuesta aleatoria, sino validar estructura e invariantes.

## Historial

Cada ejecución observada se guarda separada en `history/`.

Ejemplo:

```json
{
  "history_id": "7mojos-pbb-000017",
  "provider_id": "7mojos",
  "game_id": "piggy-bank-bonanza",
  "action_id": "gamble_suit_heart",
  "response_model_id": "gamble_success_v1",
  "observed_at": "2026-09-29T00:00:00Z",
  "previous_history_id": "7mojos-pbb-000016",
  "previous_action_id": "normal_spin",
  "request": {},
  "response": {},
  "resulting_state": "ROUND_FINISHED",
  "validation": {
    "status": "OBSERVED"
  }
}
```

El historial debe permitir reconstruir secuencias reales completas.

Los secretos deben permanecer redactados también en historial.

## index.json

Índice rápido del juego.

Debe permitir resolver sin recorrer todos los archivos:

- acción -> definición;
- acción -> modelo de respuesta;
- acción -> historial;
- estado -> acciones permitidas;
- grupo -> acciones;
- endpoint -> acciones asociadas.

Ejemplo:

```json
{
  "game_id": "piggy-bank-bonanza",
  "actions": {
    "normal_spin": {
      "definition": "result.json#actions.normal_spin",
      "response_model": "responses/spin-normal-success-v1.json"
    }
  },
  "states": {
    "IDLE": ["normal_spin"],
    "WIN_PENDING_GAMBLE": [
      "gamble_color_red",
      "gamble_color_black",
      "gamble_suit_spade",
      "gamble_suit_heart",
      "gamble_suit_diamond",
      "gamble_suit_club",
      "collect"
    ]
  },
  "history_by_action": {}
}
```

## Estados de cobertura

Por juego:

- `COMPLETE`: spin, apuestas, todas las compras, todas las elecciones y continuaciones demostradas.
- `PARTIAL`: protocolo central funciona, pero falta al menos una rama.
- `BLOCKED`: protección externa impide completar.
- `ERROR`: fallo de tooling/runtime impide análisis.

Por acción:

- `COMPLETE`
- `INCOMPLETE`
- `BLOCKED`
- `ERROR`

## Estado de evidencia

Cada dato relevante debe poder distinguir:

- `observed`
- `inferred`
- `validated`

`validated` significa que la request fue reproducida posteriormente contra el servidor y pasó el modelo/invariantes esperados.

## Validación futura

El validador futuro deberá:

1. cargar `provider.json`, `index.json` y `result.json`;
2. resolver la acción pedida;
3. verificar `requires_state` y precondiciones;
4. obtener campos dinámicos desde una sesión viva;
5. construir la request exacta;
6. enviarla;
7. guardar request y response en historial;
8. validar contra `response_model_id`;
9. actualizar estado a `validated` cuando corresponda;
10. no ejecutar acciones fuera de estado.

## Aprendizajes iniciales a conservar

### 7Mojos / Horances

Arquitectura observada:

`7mojos.com -> de-clb.horances.com -> de-cgm.horances.com -> de-se.horances.com`

Direct game client:

`gameToken=<token>`

Endpoints observados:

- `POST /api/v2/spin/placebet`
- `POST /api/v2/spin/gamble`

Campos típicos de placebet:

- `linesCount`
- `betPerLine`
- `usingOperatorFreeSpins`
- `spinMode`

Normal:
`spinMode=null`

Compra observada en Hidden Treasures:
`spinMode="BB100"`

Gamble observado en Piggy Bank Bonanza:

- `choiceType=0`: color x2
- `choiceType=1`: palo x4
- `option`: elección concreta

El dominio de `option` no debe darse por completo hasta probar todas las opciones visibles.

### Yggdrasil

Normal wager observado:

`POST https://demo.yggdrasilgaming.com/game.web/service?fn=play`

Request:
`application/x-www-form-urlencoded`

Campos observados:

- `gameid`
- `amount`
- `coin`
- `currency`
- `lang`
- `clientinfo`

Las sesiones paralelas del demo pueden disparar rate limiting. Preferir un navegador salvo evidencia de que el paralelismo es seguro.

## Criterio de éxito

Dentro de varios meses debe ser posible abrir el repositorio y responder sin reanalizar el juego:

- qué acciones existen;
- cuándo se puede ejecutar cada una;
- qué request exacta enviar;
- qué significa para una persona;
- qué respuesta esperar;
- qué campos son variables;
- qué acciones vienen después;
- qué evidencia histórica respalda la clasificación;
- qué está observado, inferido o validado;
- qué ramas siguen incompletas.
