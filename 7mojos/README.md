# 7Mojos / Horances

Arquitectura observada:

`7mojos.com -> de-clb.horances.com -> de-cgm.horances.com -> de-se.horances.com`

## Juegos

| Juego | Token | Estado | Modos relevantes |
|---|---|---|---|
| Hidden Treasures | ht | **COMPLETE** | spin, stake mapping, Buy Free Spins x100, 10 free spins, complete_spin |
| Piggy Bank Bonanza | pbb | **COMPLETE** | spin, stake mapping, gamble color 2/2, gamble suit 4/4, collect |
| Maids Cafe Riches | mcr | PARTIAL | spin, stake mapping, gamble color 2/2, collect, bonus natural pendiente |

## Endpoints comunes observados
- POST `/api/v2/spin/placebet?1.15.2.2390`
- POST `/api/v2/spin/gamble?1.15.2.2390`
- POST `/api/v2/spin/completespin?1.15.2.2390`

## Convenciones confirmadas
- Cliente directo con `gameToken=<token>`.
- JSON para las acciones observadas.
- `Authorization` es dinámico y debe salir de una sesión viva.
- Gamble color usa `choiceType=0`.
- Gamble suit usa `choiceType=1`.
- Hidden Treasures usa `spinMode=BB100` para Buy Free Spins x100.
- Las acciones sólo deben reproducirse cuando el estado/precondición correspondiente está activo.
