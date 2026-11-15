# bda-ut1-pentaho-hotel

Práctica integradora de la UT1 de Big Data Aplicado: informe de cobros y control de reservas sin cobro de un grupo hotelero de Cantabria, hecho con Pentaho Data Integration.

- **Autor:** Iván González Hernández
- **Objetivo:** a partir de tres CSV (reservas, cobros y hoteles), generar un informe de cobros por hotel y canal y un listado de reservas sin cobro, validarlos y publicarlos con un procedimiento repetible que no publica nada si algo falla.

## Para gerencia

En el cierre `cierre_aula_01` se han revisado las 20 reservas de los 5 hoteles del grupo; el conjunto de datos no incluye fechas, así que no corresponde a un periodo concreto. 15 reservas tienen un cobro registrado, por un total de **3.965,50 €**, repartido por hotel y canal de venta en `salida/informe_hotel_canal.csv`. Las otras 5 reservas, con un importe de 639,90 €, no tienen cobro asociado y se listan aparte en `salida/reservas_sin_cobro.csv` para revisión interna: que no figure un cobro en estos datos no demuestra que haya un impago. El informe no incluye la ocupación de los hoteles, porque no se dispone de datos de habitaciones.

## Versiones

| Herramienta | Versión |
|---|---|
| Pentaho Data Integration (Community) | 9.4.0.0-343 |
| Java | OpenJDK 11.0.32.1 (Temurin), indicado a PDI con la variable `PENTAHO_JAVA_HOME` |
| Sistema | Windows 11 |

## Datos

- **Código asignado:** A010
- **Conjunto:** `cierre_aula_01`
- **Procedencia:** paquete personal entregado por el profesor. Los tres CSV están en `entrada/` sin modificar y su sha256 coincide con el de `asignacion.json`.

| Archivo | Separador | Filas |
|---|---|---|
| `entrada/reservas_pdi.csv` | `,` | 20 |
| `entrada/cobros_pdi.csv` | `;` | 15 |
| `entrada/hoteles_pdi.csv` | `;` | 5 |

Todos están en UTF-8, con cabecera, punto decimal y sin separador de miles.

## Estructura

| Carpeta | Contenido |
|---|---|
| `entrada/` | CSV originales del paquete asignado, sin modificar |
| `procesos/` | Cuatro transformaciones (`.ktr`) y el job (`.kjb`) |
| `temporal/` | Salidas provisionales; no se versiona y se crea sola al ejecutar |
| `salida/` | Informes publicados |
| `evidencias/` | Diseño previo, pruebas y logs |

### Procesos

| Archivo | Qué hace |
|---|---|
| `informe_cobros.ktr` | Une reservas y cobros (Merge join INNER por `id_reserva`), añade el hotel (INNER por `id_hotel`), agrupa por hotel y canal y escribe `temporal/informe_hotel_canal.csv` |
| `reservas_sin_cobro.ktr` | Une reservas y cobros con LEFT OUTER, se queda con las que no tienen cobro (`id_reserva_cobro` nulo), añade el hotel y el motivo y escribe `temporal/reservas_sin_cobro.csv` |
| `validar_informe.ktr` | Exige 13 grupos, 15 reservas y 3965.50 €; si no cuadra, aborta |
| `validar_sin_cobro.ktr` | Exige 5 filas, 5 identificadores distintos y entre 1 y 20; si no cuadra, aborta |
| `cierre_hotel.kjb` | Comprueba las entradas, ejecuta los informes, los valida y solo entonces los copia a `salida/` |

## Cómo reproducirlo desde una copia nueva

1. Clonar el repositorio:

   ```
   git clone https://github.com/IvanGonzalezHernandez/bda-ut1-pentaho-hotel.git
   ```

2. Tener PDI 9.4 con Java 11. Si el Java del sistema es otro, indicar el de PDI con `PENTAHO_JAVA_HOME`.

3. Ejecutar el job con Kitchen desde la carpeta de PDI (en Windows, `cmd`):

   ```
   cd /d C:\ruta\a\data-integration
   Kitchen.bat /file:C:\ruta\al\repo\bda-ut1-pentaho-hotel\procesos\cierre_hotel.kjb /level:Basic
   ```

4. Resultado esperado:
   - En el log aparecen «Informe validado: 13 grupos, 15 reservas, 3965.50 €», «Reservas sin cobro validadas: 5 filas, sin duplicados» y «Cierre cierre_aula_01 publicado correctamente».
   - `salida/informe_hotel_canal.csv` tiene 13 filas y `salida/reservas_sin_cobro.csv` tiene 5 (reservas 4, 9, 11, 15 y 18).

Todas las rutas son relativas al propio proceso (`${Internal.Transformation.Filename.Directory}` y `${Internal.Job.Filename.Directory}`), así que funciona en cualquier carpeta donde se clone. Las transformaciones también se pueden lanzar sueltas con `Pan.bat /file:...`.

## Decisiones de diseño

El diseño previo completo está en [evidencias/diseno.md](evidencias/diseno.md). En resumen:

- **Proceso por lotes**: es un cierre con los datos completos en archivos, y repetirlo con las mismas entradas da el mismo resultado.
- **Se suma `cobrado`, no `importe`**: el informe es de lo cobrado, no del precio de las reservas.
- **Las reservas sin cobro se detectan por la falta de cobro** (`id_reserva_cobro` nulo tras el LEFT OUTER), no por `cobrado = 0`, y no se inventan importes: el listado muestra el precio de la reserva.
- **Provisional y publicado separados**: las transformaciones escriben en `temporal/` y el job solo copia a `salida/` si las dos validaciones pasan. Si falta una entrada o falla una validación, no se publica nada y se conserva el último informe bueno.
- **Repetible**: las salidas se sobrescriben, nunca se añaden filas.
- **Copia no atómica**: si falla la segunda copia, el job termina con error y avisa de un posible estado parcial en `salida/`.
- **CSV** porque es sencillo y legible; para un histórico grande consultado por columnas se usaría Parquet.

## Comprobación y pruebas

La validación manual (esperado, obtenido y conclusión, con el contraste grupo a grupo con la tabla de referencia del taller 2) está en [evidencias/comprobacion.md](evidencias/comprobacion.md).

Las seis pruebas obligatorias y una adicional de fallo en la copia, con lo esperado, lo obtenido y su log, están en [evidencias/pruebas.md](evidencias/pruebas.md).

## Fuentes y ayudas

- Apuntes de clase de la UT1.
- Un manual resumido elaborado con Claude (asistente de IA de Anthropic), con el que me he guiado paso a paso al construir y revisar los procesos.
