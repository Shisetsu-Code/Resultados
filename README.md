# Resultados

Repositorio canónico de protocolos observados en juegos demo/test autorizados.

## Estados
- COMPLETE: todas las ramas relevantes demostradas.
- PARTIAL: protocolo central demostrado, pero falta al menos una rama.
- BLOCKED: una protección externa impidió completar.
- ERROR: fallo de tooling/runtime.

## Evidencia
- observed: visto realmente en tráfico.
- inferred: deducido, todavía no demostrado.
- validated: reproducido contra el servidor y verificado.

Cada proveedor contiene un índice, y cada juego separa:
- README.md: explicación humana;
- result.json: acciones y requests canónicas;
- index.json: relaciones estado -> acción;
- responses/: modelos esperados;
- history/: muestras observadas.
