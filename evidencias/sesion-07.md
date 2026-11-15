# Sesión 7 — 2026-11-15

## Hecho
- Job `cierre_hotel.kjb` terminado: copias de `temporal/` a `salida/` con sobrescritura, Abortar trabajo si falla la primera copia («no se ha publicado nada nuevo») o la segunda («posible estado parcial»), registro de éxito y Éxito.
- Ejecución completa con Kitchen: valida y publica las dos salidas.
- Las seis pruebas obligatorias y una adicional de fallo en la copia, con sus logs (`evidencias/pruebas.md`).
- Tabla de comprobación manual y contraste grupo a grupo con la tabla de referencia del taller 2 (`evidencias/comprobacion.md`).
- README completo con el párrafo para gerencia.
- Revisión final contra el enunciado y cambios: `validar_sin_cobro.ktr` exige ahora exactamente los identificadores 4, 9, 11, 15 y 18; nota con autor y pregunta en el lienzo de los cinco procesos; separador de miles vacío en la lectura de importes; motivo «Sin cobro asociado en el archivo recibido». Pruebas repetidas con la versión final.

## Problemas
- Las dos copias se habían guardado sin origen y sin «reemplazar ficheros existentes».
- Kitchen añade al log en vez de sobrescribirlo; los logs se borraron antes de repetir las pruebas.

## Comprobado
- Ejecución completa del job con Kitchen y las seis pruebas obligatorias.
- Tabla de comprobación y contraste grupo a grupo con la referencia del taller 2.
- Clonado del repositorio en otra carpeta y ejecución del job con Kitchen: valida y publica.

## Ayuda recibida
- Apuntes de clase de la UT1.
- Claude: revisión del job, ejecución de las pruebas con Kitchen y Pan, redacción de `pruebas.md`, `comprobacion.md`, los registros de sesión y el README, y revisión del cumplimiento del enunciado con los cambios finales.

## Pendiente
- Entregar en Moodle la URL y el commit, y preparar la defensa.

## Avance
- Commit: `2c6af75`
