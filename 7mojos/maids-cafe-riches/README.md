# Maids Cafe Riches

Estado actual: **PARTIAL**

## Datos base
- Proveedor: 7Mojos / Horances
- gameToken: `mcr`
- 5 reels x 4 filas
- 40 líneas
- RTP publicado: 96.41%
- Volatilidad publicada: High
- Features publicadas: Bonus, Wild

## Spin normal

### Lenguaje natural
Tirada normal de 40 líneas.

### Lenguaje del juego
```json
{
  "linesCount": 40,
  "betPerLine": 0.1,
  "usingOperatorFreeSpins": false,
  "spinMode": null
}
```

Total observado: 40 x 0.10 = 4.00 PLM.

También se validó desde UI:
- 40 x 0.20 = 8.00 PLM.

Endpoint:
`POST https://de-se.horances.com/api/v2/spin/placebet?1.15.2.2390`

## Gamble color

El servidor habilita:
- RED x2 -> `choiceType=0, option=0`
- BLACK x2 -> `choiceType=0, option=1`

Cobertura: **2/2 COMPLETE**

Endpoint:
`POST https://de-se.horances.com/api/v2/spin/gamble?1.15.2.2390`

## Collect / completar ronda

Cuando existe una ganancia pendiente y no se desea seguir con Gamble:

`POST https://de-se.horances.com/api/v2/spin/completespin?1.15.2.2390`

Body observado: `null`.

## Bonus natural

La página oficial indica una feature Bonus activada por símbolo Bonus. No existe botón de compra visible.

Se ejecutaron muestras adicionales, incluso en sesiones paralelas, sin observar todavía la transición del bonus en tráfico.

Estado: **INCOMPLETE**

No se inventa un comando para esta feature: hasta observarla, queda modelada como una transición posible desde `normal_spin`, no como request independiente.

## Máquina de estados

```text
IDLE
  -> normal_spin
       -> ROUND_FINISHED
       -> WIN_PENDING_GAMBLE
            -> gamble_color_red
            -> gamble_color_black
            -> collect
       -> BONUS_RUNNING   [INCOMPLETE / no observado]
```

## Validación posterior

Las acciones listas para replay con sesión viva son:
- normal_spin
- normal_spin_stake_8
- gamble_color_red
- gamble_color_black
- collect

El header `Authorization` siempre debe obtenerse de una sesión viva.


## Bonus natural: relación confirmada en el cliente

El bundle específico del juego muestra que `resolveBonusGame()` recibe el bonus desde `steps[].extraFeatures` de la respuesta del spin y lo anima directamente.

```text
normal_spin
  -> response.steps[].extraFeatures
       -> BONUS_RUNNING
       -> vuelta al flujo de la ronda
```

La API genérica del cliente contiene `/spin/skillbonusresult`, pero no encontré una llamada a ese endpoint en el flujo específico de Maids Cafe Riches. Por seguridad **no se indexa como acción ejecutable de este juego**.

Falta observar una respuesta live con `extraFeatures` no vacío para pasar esta rama de `inferred` a `observed`.
