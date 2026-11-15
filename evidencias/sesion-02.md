# Sesión 2 — 2026-10-26

## Hecho
- `informe_cobros.ktr`: lectura de reservas, cobros y hoteles; ordenación por `id_reserva` y Merge join INNER (15 filas); ordenación por `id_hotel` y Merge join INNER con hoteles (15 filas).
- Inicio de la agrupación por hotel y canal.

## Problemas
- La primera versión del `.ktr` se guardó vacía y hubo que rehacer la lectura.
- Los dos Ordenar filas estaban en descendente y el Merge join fallaba; se pusieron en ascendente.
- Una conexión entre pasos quedó desactivada y el paso `Leer hoteles` no tenía los campos cargados.
- En el Agrupar por, `n_reservas` quedó sin tipo de agregación.

## Pendiente
- Terminar la agrupación y escribir el CSV provisional.

## Avance
- Commit: `59a012c`
