# Golden Vegas 3x3

Estado: **PARTIAL**

- gameToken: `gv3x3`
- 3x3
- líneas: 5
- RTP: 94.2
- volatilidad: Medium
- features: Wild, Free Spins, Bonus Wheel, Buy Free Spins

## Observado

- Normal spin observed: POST /api/v2/spin/placebet HTTP 200.
- Displayed total bet: 0.25 PLM = 5 x 0.05.
- BUY FREE SPINS UI observed at cost 25.00 PLM for 0.25 PLM base bet (x100).
- Purchase visibly started 12 Free Spins; balance decreased consistently by 25.00 PLM.
- The purchase request body was not retained in Firetrace capture and is therefore not marked observed.
- Page states 3 scatters trigger 12 Free Spins x4 and there is a Bonus Wheel; Bonus Wheel path not captured.
