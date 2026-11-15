# Tabla de comprobación (validación manual) — cierre_aula_01 (A010)

Comprobación hecha el 2026-11-15 sobre los archivos completos ya escritos en `salida/` y sobre el log de la primera ejecución del job ([prueba1_primera_ejecucion.log](prueba1_primera_ejecucion.log)).

## Valores de referencia

| Comprobación | Esperado | Obtenido | Dónde se ha mirado | Conclusión |
|---|---|---|---|---|
| Filas leídas de reservas | 20 | 20 | Log: `Leer reservas` W=20 | ✅ |
| Filas leídas de cobros | 15 | 15 | Log: `Leer cobros` W=15 | ✅ |
| Filas leídas de hoteles | 5 | 5 | Log: `Leer hoteles` W=5 | ✅ |
| Filas tras unir reservas y cobros (INNER) | 15 | 15 | Log: `Unir reservas-cobros` W=15 | ✅ |
| Filas tras añadir hoteles | 15 | 15 | Log: `Unir hoteles` W=15 | ✅ |
| Grupos en el informe | 13 | 13 | `salida/informe_hotel_canal.csv` (13 filas más la cabecera) | ✅ |
| Suma de `n_reservas` | 15 | 15 | Suma de la columna en `salida/informe_hotel_canal.csv` | ✅ |
| Suma de `cobrado_total` | 3965.50 € | 3965.50 € | Suma de la columna en `salida/informe_hotel_canal.csv` | ✅ |
| Laredo, canal web | 2 reservas, 496.50 € | 2 reservas, 496.50 € | Fila `Laredo;web` del informe | ✅ |
| Reservas sin cobro | 5: ids 4, 9, 11, 15 y 18 | 5: ids 4, 9, 11, 15 y 18 | `salida/reservas_sin_cobro.csv` | ✅ |
| Duplicados en sin cobro | 0 | 0 | `id_reserva` del listado | ✅ |
| Reconciliación | 15 + 5 = 20 | 15 cobradas + 5 sin cobro = 20 reservas | Informe y listado | ✅ |

## Contraste grupo a grupo con la tabla de referencia del taller 2

Un total correcto puede ocultar importes asignados al hotel equivocado, así que se compara cada grupo.

| Hotel | Canal | Referencia taller 2 | Obtenido en `salida/` | Conclusión |
|---|---|---|---|---|
| Comillas | ota | 1 · 132.00 | 1 · 132.00 | ✅ |
| Comillas | recepcion | 1 · 525.00 | 1 · 525.00 | ✅ |
| Comillas | web | 2 · 642.00 | 2 · 642.00 | ✅ |
| Laredo | ota | 1 · 124.00 | 1 · 124.00 | ✅ |
| Laredo | web | 2 · 496.50 | 2 · 496.50 | ✅ |
| Noja | ota | 1 · 195.00 | 1 · 195.00 | ✅ |
| Noja | web | 1 · 280.00 | 1 · 280.00 | ✅ |
| Potes | ota | 1 · 260.00 | 1 · 260.00 | ✅ |
| Potes | recepcion | 1 · 201.00 | 1 · 201.00 | ✅ |
| Potes | web | 1 · 150.00 | 1 · 150.00 | ✅ |
| Santander | ota | 1 · 390.00 | 1 · 390.00 | ✅ |
| Santander | recepcion | 1 · 410.00 | 1 · 410.00 | ✅ |
| Santander | web | 1 · 160.00 | 1 · 160.00 | ✅ |

Los 13 grupos coinciden exactamente con la referencia.

## Listado de reservas sin cobro

| id_reserva | Hotel | Canal | Importe (precio de la reserva) |
|---|---|---|---|
| 4 | Santander | web | 89.90 |
| 9 | Noja | ota | 140.00 |
| 11 | Laredo | recepcion | 72.00 |
| 15 | Noja | recepcion | 268.00 |
| 18 | Potes | ota | 70.00 |

Se ha comparado cada fila con `entrada/reservas_pdi.csv` y `entrada/hoteles_pdi.csv`: el hotel, el canal y el importe coinciden. El importe es el precio de la reserva (639.90 € en total), no un importe cobrado ni adeudado.

## Conclusión

Todos los valores de referencia y los 13 grupos coinciden. Las dos salidas son correctas.
