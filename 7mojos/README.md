# 7Mojos / Horances

Arquitectura observada:

`7mojos.com -> de-clb.horances.com -> de-cgm.horances.com -> de-se.horances.com`

## Juegos

| Juego | Token | Estado | Modos relevantes |
|---|---|---|---|
| Hidden Treasures | ht | PARTIAL | spin, Buy Free Spins x100 |
| Piggy Bank Bonanza | pbb | PARTIAL | spin, gamble color x2, gamble suit x4 |
| Maids Cafe Riches | mcr | PARTIAL | spin, gamble color x2, collect, bonus natural pendiente |

## Endpoints comunes observados
- POST `/api/v2/spin/placebet?1.15.2.2390`
- POST `/api/v2/spin/gamble?1.15.2.2390`
- POST `/api/v2/spin/completespin?1.15.2.2390`

Las requests usan Authorization dinámico de sesión; nunca se guarda el valor real.
