# Hidden Treasures

Estado: **COMPLETE**

## Datos base
- Proveedor: 7Mojos / Horances
- gameToken: `ht`
- 5 reels x 3 filas
- 30 líneas
- Buy Free Spins disponible
- No se observó Gamble en este título.

## Spin normal

Endpoint:
`POST https://de-se.horances.com/api/v2/spin/placebet?1.15.2.2390`

### Ejemplo 1
```json
{
  "linesCount": 30,
  "betPerLine": 0.2,
  "usingOperatorFreeSpins": false,
  "spinMode": null
}
```

Lenguaje natural:
30 líneas x 0.20 = 6.00 PLM.

### Ejemplo 2
```json
{
  "linesCount": 30,
  "betPerLine": 0.5,
  "usingOperatorFreeSpins": false,
  "spinMode": null
}
```

Lenguaje natural:
30 líneas x 0.50 = 15.00 PLM.

## Buy Free Spins x100

En la UI, con apuesta base de 15.00 PLM:
- costo mostrado: 1500.00 PLM;
- relación: 100 x apuesta base.

Request observada:

```json
{
  "linesCount": 30,
  "betPerLine": 0.5,
  "usingOperatorFreeSpins": false,
  "spinMode": "BB100"
}
```

Endpoint:
`POST https://de-se.horances.com/api/v2/spin/placebet?1.15.2.2390`

La compra inicia 10 Free Spins.

## Relación de estados

```text
IDLE
  -> normal_spin
       -> ROUND_FINISHED

IDLE
  -> buy_free_spins_x100
       -> FREE_SPINS_INTRO
       -> FREE_SPINS_RUNNING
       -> FREE_SPINS_RESULT
       -> complete_spin
       -> IDLE
```

No apareció ninguna pantalla de elección dentro de la compra.
Cobertura de elecciones: **0 esperadas / 0 pendientes**.

El click "Press anywhere to continue" es una transición local de UI: no produjo una request adicional al servidor.

## Completar ronda

Al terminar los Free Spins:

`POST https://de-se.horances.com/api/v2/spin/completespin?1.15.2.2390`

Body:
`null`

La request fue observada con HTTP 200.

## Nota de captura

La versión actual de Firetrace marcó los response bodies grandes de `placebet` como `unavailable_unbounded`, pero el request, status HTTP y flujo visual fueron observados. El modelo de respuesta se conserva también de una captura live anterior del mismo juego donde se observó `special.freespins.spinsCount = 10` y el listado de spins.
