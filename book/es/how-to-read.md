---
title: "Cómo leer este libro"
---

## Qué nos hará falta

1. **Un ordenador** con Windows, macOS o Linux.
2. **QGIS 4.2 o posterior.** Se descarga gratis en qgis.org. Todo el código lo hemos comprobado en la versión 4.2. No hace falta instalar Python aparte, porque ya viene dentro de QGIS.
3. **Internet.** Todos los datos del libro son abiertos. Los descargamos sobre la marcha.

Y ya está. No hace falta saber Python de antes. Basta con lo que ya sabemos hacer: trabajar en QGIS con el ratón.

## Cómo está organizado el libro

El libro sigue el mismo orden en el que crece nuestro proyecto.

1. **Primero, el prólogo y el pliego de condiciones.** Vemos cuál es el problema y decidimos qué tiene que saber hacer exactamente nuestra herramienta. Aquí todavía no hay código.
2. **Después, el capítulo 2, de introducción:** «Introducción a Python» e «Introducción a PyQGIS». En ellos está solo lo más básico: de qué se compone el código y cómo es QGIS por dentro. Sin esto, más adelante no se entendería por qué el código está escrito precisamente así.
3. **Luego, los capítulos del proyecto, a partir del capítulo 3.** Un capítulo, una etapa del trabajo: el límite del municipio, las líneas eléctricas, las casas, las distancias, el tiempo de viaje, etcétera. Al final de cada capítulo tenemos un resultado nuevo.
4. **Al final, la herramienta terminada.** Un plugin de QGIS que lo calcula todo para cualquier municipio.

**Una lección, un paso.** Los pasos son pequeños. Si un paso resulta complicado, no lo comprimimos: lo repartimos en varias lecciones.

## Cómo es una lección

Todas las lecciones tienen las mismas partes. Así es más fácil orientarse: siempre sabemos dónde estamos y qué viene después.

1. **Dónde lo dejamos.** Un repaso breve, para no tener que volver atrás.
2. **Qué queremos entender.** La pregunta de la lección. Ahí mismo anotamos qué esperamos ver y cómo sabremos si nos hemos equivocado. Esto lo decidimos antes del código. No adelantamos cifras: solo nos las dará el cálculo.
3. **Los pasos.** Todos siguen el mismo esquema:
   - primero, con palabras, qué queremos hacer;
   - luego, unas pocas líneas de código;
   - después, el repaso: qué hace cada línea;
   - y por qué está escrita precisamente así: de dónde sale cada nombre y dónde podemos encontrarlo por nuestra cuenta.
4. **Qué ha salido.** Comparamos el resultado con lo que esperábamos. Si no coincide, eso también es un resultado, y averiguamos por qué ha pasado.
5. **Si algo falla.** Los errores que nos encontramos nosotros y cómo dimos con su causa.
6. **Tus datos.** Qué cambiar para calcular nuestro propio municipio.
7. **Resumen.** Los conceptos nuevos, el código nuevo y lo esencial.
8. **Secretos del programador.** Cómo encontrar o recordar por nuestra cuenta el comando que necesitamos cuando no tenemos el libro a mano.
9. **Preguntas para un ingeniero.** Dos o tres preguntas para comprobar que lo hemos entendido. La respuesta está justo debajo de cada pregunta. Mejor pensarla primero y abrir la respuesta después.
10. **Ejercicios.** Tres ejercicios cortos para fijar lo aprendido. Las soluciones están al final del libro, en «Soluciones de los ejercicios», y debajo de cada ejercicio hay un enlace.
11. **La pregunta abierta.** Con ella empieza la lección siguiente.

## Cómo trabajar con el código

**Tecleamos el código a mano, no lo copiamos.** Así se recuerda antes: la mano retiene lo que la cabeza todavía no ha tenido tiempo de entender.

**Equivocarse es normal.** Si el código no funciona, primero lo comparamos con el libro letra a letra. Casi siempre la culpa es de un paréntesis, unas comillas o un espacio al principio de la línea que faltan. Python siempre dice en qué línea está el error y qué ha ido mal. Desde la primera lección aprendemos a leer esos mensajes.

**Las teclas.** En el libro las teclas se indican para Windows y Linux. En macOS, en lugar de Ctrl solemos pulsar Cmd, y en lugar de Alt, Option.

**Todo el código del libro es abierto.** Se puede coger, cambiar y usar en el trabajo con la licencia Apache 2.0. El texto y las figuras del libro se publican con licencia CC BY 4.0.

## Si algo no funciona

QGIS se actualiza, y los datos de internet a veces cambian de dirección. Si un código del libro ha dejado de funcionar o encontramos un error, podemos escribirlo en la página del proyecto en GitHub, en la sección Issues. Allí mismo se explica qué hay que indicar para que el error sea fácil de localizar. Así el libro mejora para todos.
