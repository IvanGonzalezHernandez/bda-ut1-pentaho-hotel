# Sesión 4 — 2026-11-09

## Hecho
- `validar_informe.ktr` terminado: Agrupar por con «siempre devolver una fila», Filtrar filas que exige 13 grupos, 15 reservas y 3965.50 €, rama válida con Escribir a log y rama inválida con Abortar (umbral 0).
- Probado con el informe real (valida), con un CSV con solo la cabecera (aborta) y con un total alterado (aborta).

## Problemas
- El Agrupar por tenía campos de grupo y devolvía 13 filas en vez de una.
- El mensaje del log no se guardaba porque se escribía en otro sitio del diálogo.

## Pendiente
- `validar_sin_cobro.ktr`.

## Avance
- Commit: `5bd657b`
