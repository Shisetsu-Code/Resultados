# Piggy Bank Bonanza

Estado: **COMPLETE**

## Datos base
- Proveedor: 7Mojos / Horances
- gameToken: `pbb`
- 5 reels x 4 filas
- 40 líneas
- RTP publicado: 96.72%
- Volatilidad: Medium
- Features publicadas: Wild, Scatter
- No se observó compra de bonus.

## Spin normal
Endpoint:
`POST https://de-se.horances.com/api/v2/spin/placebet?1.15.2.2390`

Ejemplos observados:
- 40 x 0.03 = 1.20 PLM
- 40 x 0.05 = 2.00 PLM

## Gamble color x2
Endpoint:
`POST /api/v2/spin/gamble?1.15.2.2390`

Mapa completo:
- RED -> `choiceType=0, option=0`
- BLACK -> `choiceType=0, option=1`

Cobertura: **2/2**

## Gamble palo x4
Mapa completo:
- SPADE -> `choiceType=1, option=0`
- CLUB -> `choiceType=1, option=1`
- HEART -> `choiceType=1, option=2`
- DIAMOND -> `choiceType=1, option=3`

Cobertura: **4/4**

## Collect
`POST /api/v2/spin/completespin?1.15.2.2390`
Body: `null`

## Estados
```text
IDLE
  -> normal_spin
      -> ROUND_FINISHED
      -> WIN_PENDING_GAMBLE
          -> gamble_color_red / black
          -> gamble_suit_spade / club / heart / diamond
          -> collect
```

Todas las ramas visibles de gamble fueron observadas y mapeadas.
