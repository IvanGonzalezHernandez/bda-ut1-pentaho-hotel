# Diseño previo — cierre_aula_01 (A010)

## 1. Qué representa cada fila

- `entrada/reservas_pdi.csv` → una reserva: hotel, canal, noches e `importe` (precio de la reserva). 20 filas
- `entrada/cobros_pdi.csv` → el cobro de una reserva (`medio` y `cobrado`). Como máximo uno por reserva. 15 filas
- `entrada/hoteles_pdi.csv` → un hotel del grupo: nombre, comarca y cadena. 5 filas
- `salida/informe_hotel_canal.csv` → una combinación de hotel y canal con al menos una reserva cobrada: nº de reservas cobradas e importe cobrado. 13 filas
- `salida/reservas_sin_cobro.csv` → una reserva sin cobro asociado, con su precio y el motivo. 5 filas

## 2. Dónde se guarda cada cosa

- **Originales:** `entrada/`. No se modifican nunca; su sha256 está en `asignacion.json`.
- **Provisionales:** `temporal/`. Las transformaciones escriben aquí y no se sube al repositorio.
- **Publicados:** `salida/`. Solo se copian aquí los CSV que han pasado las validaciones.

## 3. Por qué un proceso por lotes

- Es un **cierre de periodo**: dirección necesita el informe una vez cerrado, no en tiempo real.
- Los datos llegan **completos en archivos**, no como un flujo continuo de eventos.
- El volumen es pequeño y el proceso termina en segundos.
- Se puede **repetir** con las mismas entradas y obtener el mismo resultado, lo que facilita comprobarlo.

## 4. Comprobaciones, repetición y entradas que faltan

**Antes de empezar:** el job comprueba que existen las tres entradas. Si falta alguna, aborta indicando cuál y no publica nada.

**Antes de publicar** (validación automática):
- Informe: 13 grupos, 15 reservas en total y 3965.50 € cobrados. No puede tener solo la cabecera.
- Sin cobro: 5 filas, identificadores válidos y sin duplicados.
- Reconciliación: 15 cobradas + 5 sin cobro = 20 reservas.

Si alguna validación falla, el job termina con error y no copia nada a `salida/`.

**Al repetir:** los CSV se sobrescriben, no se añaden filas. Deben salir los mismos grupos, filas y totales.

**Si falla una copia a `salida/`:** el job termina con error y avisa de un posible estado parcial (un archivo nuevo y otro antiguo), porque copiar dos archivos no es una operación atómica.

## 5. Por qué CSV y alternativa para un histórico grande

**CSV** porque es texto plano, se abre en Excel o en cualquier editor, no necesita base de datos y es suficiente para unos pocos cientos de filas.

Para un **histórico grande consultado por columnas** usaría **Parquet**:
- formato columnar: solo se leen las columnas que necesita la consulta;
- comprimido, ocupa mucho menos que CSV;
- guarda los tipos de datos (números, fechas…), así que no hay errores de separadores ni de decimales;
- se puede particionar (por ejemplo, por año y hotel) y lo leen Spark, Hive, etc.

## 6. Esquema del flujo

    entrada/ (3 CSV)
       │
       ▼
    [1] ¿Existen las 3 entradas? ──no──► Abort (indica cuál falta)
       │ sí
       ▼
    [2] informe_cobros.ktr ──────► temporal/informe_hotel_canal.csv
       ▼
    [3] reservas_sin_cobro.ktr ──► temporal/reservas_sin_cobro.csv
       ▼
    [4] ¿Existen las 2 salidas provisionales? ──no──► error, no se publica
       ▼
    [5] validar_informe.ktr + validar_sin_cobro.ktr ──falla──► Abort, no se publica
       │ ok
       ▼
    [6] Copiar los 2 CSV de temporal/ a salida/ ──falla──► error + aviso de estado parcial
       │ ok
       ▼
    [7] Registrar éxito en el log