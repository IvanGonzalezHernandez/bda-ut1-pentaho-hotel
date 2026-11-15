# Pruebas obligatorias — cierre_aula_01 (A010)

Ejecutadas el 2026-11-15 con PDI 9.4.0.0-343 (Kitchen para el job, Pan para las validaciones).
Los originales de `entrada/` no se han modificado: tras las pruebas su sha256 coincide con `asignacion.json`.

| # | Prueba | Qué se hizo | Esperado | Obtenido | Log | Conclusión |
|---|---|---|---|---|---|---|
| 1 | Primera ejecución | `temporal/` vacío y job completo con Kitchen | Dos salidas correctas y éxito | Validaciones superadas, «Cierre cierre_aula_01 publicado correctamente», `salida/` con 13 grupos y 5 reservas sin cobro | [prueba1](prueba1_primera_ejecucion.log) | ✅ |
| 2 | Repetición | Segunda ejecución del job sin cambiar nada | Mismos grupos, filas y totales, sin duplicados | Las dos salidas son idénticas byte a byte a las de la prueba 1 (mismo md5); 14 y 6 líneas | [prueba2](prueba2_repeticion.log) | ✅ |
| 3 | Falta una entrada | Se apartó `cobros_pdi.csv` fuera del repositorio y se lanzó el job; después se devolvió | Job con error y sin publicación | `Existe cobros` falso → «Falta la entrada cobros_pdi.csv», «Finished with errors»; no se ejecuta ninguna transformación y `salida/` conserva la hora anterior | [prueba3](prueba3_falta_entrada.log) | ✅ |
| 4 | Salida incompleta | `temporal/informe_hotel_canal.csv` reducido a la cabecera; solo `validar_informe.ktr` con Pan | Validación con error | El Agrupar por devuelve una fila `[0], [null], [null]` y el paso Abortar para la transformación: «Validación del informe fallida» | [prueba4](prueba4_salida_incompleta.log) | ✅ |
| 5 | Total incorrecto | Laredo-web cambiado de 496.50 a 400.00 en la copia provisional; solo `validar_informe.ktr` | Validación con error | Llegan `[13], [15], [3869.0]` y aborta: «Validación del informe fallida» | [prueba5](prueba5_total_incorrecto.log) | ✅ |
| 6 | Recuperación | Entradas restauradas y copia provisional regenerada; job completo | Job correcto | Validaciones superadas y publicación correcta; salidas idénticas a las de la prueba 1 | [prueba6](prueba6_recuperacion.log) | ✅ |

En las pruebas 4 y 5 se alteró solo la copia provisional de `temporal/`, sin lanzar antes el job completo; después se restauró.

## Prueba adicional: fallo en una copia

Se marcó `salida/reservas_sin_cobro.csv` como solo lectura y se lanzó el job.

- `Publicar informe` copió el informe sin problemas.
- `Publicar sin cobro` falló («Acceso denegado») y el job terminó en `Publicación parcial`: «Error al copiar reservas sin cobro: posible estado parcial en salida/».
- Resultado: en `salida/` quedó un informe nuevo junto a un listado de la ejecución anterior. Es el estado parcial que avisa el job, porque copiar dos archivos no es una operación atómica.
- Después se quitó el atributo de solo lectura y la prueba 6 dejó `salida/` coherente.

Log: [prueba_extra_fallo_copia](prueba_extra_fallo_copia.log)

## Observación

Lanzados desde Git Bash, `Kitchen.bat` y `Pan.bat` devolvieron código de salida 0 también en las ejecuciones con error. El fallo se ve en el log («Finished with errors», mensajes de Abortar) y en el resultado `false` de las entradas del job.
