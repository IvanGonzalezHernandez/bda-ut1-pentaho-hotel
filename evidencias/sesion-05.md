# Sesión 5 — 2026-11-10

## Hecho
- `validar_sin_cobro.ktr`: exige 5 filas, 5 identificadores distintos (sin duplicados) e identificadores entre 1 y 20. Probado con el listado real (valida), con una reserva duplicada (aborta) y con solo la cabecera (aborta).
- Inicio del job `cierre_hotel.kjb`: comprobación de las tres entradas con su Abortar trabajo, ejecución de los dos informes y comprobación de las dos salidas provisionales.

## Problemas
- Los tipos de agregación de `n_filas` y `n_distintos` no se grababan y al cerrar Spoon se guardó una versión antigua de la transformación; se rehízo.
- El filtro tenía `=` en lugar de `>=` y `<=` y una condición vacía de más.
- Al principio las tres comprobaciones de entradas salían en paralelo desde Inicio; se encadenaron.

## Comprobado
- `validar_sin_cobro` valida con el listado real y aborta con un duplicado y con solo cabecera.
- Encadenamiento correcto de las comprobaciones de entradas en el job.

## Ayuda recibida
- Apuntes de clase de la UT1.
- Claude: guía paso a paso y revisión de los archivos. Rehízo en el archivo la lectura y los agregados de `validar_sin_cobro.ktr` tras guardarse una versión antigua.

## Pendiente
- Validaciones y publicación en el job.

## Avance
- Commits: `b786777`, `6d293ea`
