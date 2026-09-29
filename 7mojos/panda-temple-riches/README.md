# Panda Temple Riches

Estado: **PARTIAL**

- gameToken: `ptr`
- 5x3
- líneas: 30
- RTP: 95.24
- volatilidad: High
- features publicadas: Wild, Scatter, Free Spins, Gamble

## Observado

- Normal spin observed: POST /api/v2/spin/placebet HTTP 200.
- Displayed total bet: 1.50 PLM = 30 x 0.05.
- Gamble screen exposed color x2 and suit x4.
- RED observed as choiceType=0, option=0 via POST /api/v2/spin/gamble HTTP 200.
- Color BLACK and all 4 suit options remain untested for this title.
- Natural Free Spins: page states 3 scatters trigger 10 Free Spins with x3 wins; not captured live yet.

## Acciones listas para replay

- `normal_spin`
- `gamble_color_red`
