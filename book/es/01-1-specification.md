---
title: "Lección 1.1. Para quién es la herramienta y qué tiene que devolver"
---

## Dónde lo dejamos

En el prólogo vimos el mismo cuadro en dos partes de 2018. El temporal rompe las líneas, los árboles y la nieve cortan las carreteras, y la ayuda no puede llegar. No encontramos ninguna herramienta abierta que, para cualquier municipio, muestre qué casas están cerca de las líneas y cuánto se tarda en llegar hasta ellas.

Así que la construimos nosotros. Pero ¿por dónde empezamos?

Lo primero que apetece es abrir QGIS y ponerse a cargar datos. Pero así es fácil hacer bien todas las cuentas y aun así responder a otra pregunta. Por eso la primera lección es sin código. **Antes, nos ponemos de acuerdo en qué es exactamente lo que construimos.**

## Qué queremos entender

La pregunta de la lección: **¿qué tiene que devolver la herramienta para que, con su resultado, una persona pueda tomar una decisión?**

¿Cómo sabremos si nos hemos equivocado?

- Si de algún resultado no podemos decir qué hará con él el usuario, ese resultado sobra.
- Si al usuario le falta algo para decidir, es que se nos ha pasado.

Los ingenieros llaman a este documento **pliego de condiciones**. En el fondo es un acuerdo con nosotros mismos: qué construimos y cómo sabremos que lo hemos construido. En esta lección escribimos su primera mitad.

## Paso 1. Quién va a usar la herramienta

Pensemos a quién le hace falta la respuesta a nuestra pregunta. Si imaginamos un temporal de invierno en la montaña, enseguida aparecen tres protagonistas.

1. **La distribuidora.** Es la responsable de las líneas: hace la poda y tala a lo largo de los trazados, planifica las inspecciones y decide adónde manda una brigada antes de un temporal. Piensa en líneas, no en casas. Necesita saber **qué tramos de línea revisar primero**.
2. **Los bomberos y el 112.** Si cae un conductor y empieza un incendio, casi siempre los primeros en llegar son los bomberos. Necesitan saber de antemano **qué casas están junto a las líneas y cuántos minutos se tarda en llegar a ellas**, de día y de noche.
3. **El ayuntamiento.** Redacta el plan de emergencias y concede las licencias de obra. Necesita **ver todo el territorio**: en qué partes del municipio hay viviendas junto a las líneas y lejos de los bomberos.

Aquí pasa algo interesante. La pregunta es la misma para todos, pero cada uno la mira de una manera.

**Así que el cálculo es uno, pero el resultado hace falta en tres niveles: los edificios uno a uno, los tramos de línea y todo el territorio.**

## Paso 2. Qué le damos a la herramienta como entrada

Ahora pensemos qué necesita la herramienta para calcular la respuesta. Vamos a recorrer nuestra pregunta palabra a palabra.

- **«Qué casas…»:** hacen falta los edificios. Los sacaremos del Catastro, que es abierto.
- **«…están cerca de las líneas…»:** hace falta una capa de líneas eléctricas. Por defecto, datos abiertos. Pero la distribuidora puede tener su propia capa, más precisa, y la herramienta tiene que aceptarla.
- **«…en este municipio…»:** hace falta el límite del municipio.
- **«…cuánto tardan los bomberos…»:** hacen falta los parques de bomberos y las carreteras. Y además una velocidad para cada tipo de carretera: un camión de bomberos no va igual por una autovía que por una carretera de montaña llena de curvas.
- **«…qué revisar primero…»:** hace falta una regla para pasar de la distancia y el tiempo a la urgencia. La veremos en la lección 1.2.

Los edificios y las carreteras la herramienta puede descargarlos por sí misma. En cambio, el límite del municipio, la tabla de parques de bomberos, las velocidades y la regla de urgencia hay que dárselos.

Aquí hay una regla importante. **Todo lo que depende del lugar se lo damos como entrada, no lo escribimos dentro del código.** ¿Por qué?

Imaginemos que escribimos en el código una velocidad de 40 km/h, porque es la que vale para Lena. Luego alguien ejecuta la herramienta en un municipio llano. El código calcula en silencio con la misma velocidad, y el tiempo sale mal. El programa no muestra ningún error. Si, en cambio, la velocidad es una entrada, se ve y se cambia fácilmente.

**Así que en el código no puede quedar nada que solo valga para Lena.**

## Paso 3. Qué devuelve la herramienta como resultado

Volvemos a los tres usuarios y decidimos qué recibe cada uno. De paso, comprobamos cada resultado: ¿qué hará con él una persona?

1. **Capa de edificios residenciales.** Para cada edificio:
   - la distancia a la línea más cercana y la tensión de esa línea;
   - el número de viviendas del edificio;
   - el año de construcción;
   - el tiempo de viaje de los bomberos, de día y de noche;
   - la clase de urgencia.

   *Quién la usa:* los bomberos y el 112, que así saben a qué casas se tarda más en llegar.
2. **Capa de tramos de línea.** Dividimos las líneas en tramos de la misma longitud. Para cada tramo: cuántas viviendas hay cerca, cuánto se tarda en llegar a la casa más lejana y la clase de urgencia.

   *Quién la usa:* la distribuidora, que decide por dónde empezar la poda y tala.
3. **Tabla por parroquias.** La parroquia es la división del municipio con la que en Asturias se llevan las estadísticas.

   *Quién la usa:* el ayuntamiento, que ve en qué partes del territorio está el problema.
4. **Mapa de zonas de urgencia.** Es un ráster en una malla regular, sin casas individuales.

   *Quién lo usa:* el ayuntamiento y todo el que necesite ver el cuadro completo. En él no se distinguen casas concretas.
5. **Mapa de completitud de los datos de líneas.** Dónde podemos fiarnos de los datos abiertos sobre las líneas y dónde tienen huecos.

   *Quién lo usa:* todos. Que en algún sitio no haya líneas en los datos no quiere decir que no las haya en la realidad.
6. **Informe de ejecución.** Qué datos se dieron de entrada, de qué versión eran y qué versión de la herramienta hizo el cálculo.

   *Quién lo usa:* todo el que quiera comprobar o repetir el cálculo. Sin el informe, dentro de seis meses nadie sabrá con qué datos calculamos.

Cada resultado le hace falta a alguien; no sobra ninguno. Lo guardamos todo en dos formatos. GeoPackage se abre en cualquier SIG, y CSV, en cualquier hoja de cálculo.

## Paso 4. Qué hacemos con los datos privados

La capa de edificios muestra casas concretas. Es información privada, y no se puede subir a internet sin más.

Por eso acordamos lo siguiente:

- la capa con cada casa se queda en manos de quien ejecuta la herramienta;
- en lo que se publique, solo habrá totales por territorio;
- si detrás de una cifra hay solo unas pocas casas, la ocultamos, porque con ella se podría identificar una casa concreta.

## Qué ha salido

Lo comparamos con lo que queríamos saber.

Tenemos la primera mitad del pliego de condiciones:

- **tres usuarios** y lo que necesita cada uno;
- **las entradas:** el límite, las líneas, los parques de bomberos, las velocidades y la regla de urgencia. Todo lo que depende del lugar se define en la entrada;
- **seis resultados**, y de cada uno está claro quién lo usa y para qué;
- **una regla para los datos privados.**

La comprobación de «sobra o falta» de momento se cumple: cada resultado tiene un usuario.

Pero en el pliego todavía hay muchas palabras que no hemos definido. ¿Qué significa «cerca de la línea»? ¿Cómo sabemos cuántas personas viven en una casa? ¿Cómo calculamos el tiempo de viaje? ¿Cómo pasamos de la distancia y el tiempo a la urgencia? ¿Y cómo comprobamos que podemos fiarnos de la herramienta? Eso es la segunda mitad del pliego, y la lección siguiente.

## Tus datos

Para otro municipio, en la entrada hay que cambiar:

- el límite del municipio;
- la tabla de parques de bomberos: las coordenadas y los horarios se pueden sacar de las páginas públicas del servicio de emergencias;
- la capa de líneas, si hay una propia;
- las velocidades por tipo de carretera: hay que comprobar si encajan con el relieve.

Los edificios la herramienta los toma del Catastro. En España el Catastro es común a todas las comunidades salvo Navarra y el País Vasco, que tienen el suyo propio.

## Resumen

### Conceptos nuevos

- **Pliego de condiciones:** el acuerdo sobre para quién construimos la herramienta, qué le damos como entrada, qué devuelve y cómo comprobaremos el resultado. Ejemplo: en esta lección hemos anotado tres usuarios, las entradas y seis resultados.
- **Entrada:** lo que la herramienta recibe del usuario. Ejemplo: el límite del municipio.
- **Resultado:** lo que la herramienta devuelve. Ejemplo: la capa de edificios residenciales.
- **Parroquia:** la división del municipio con la que en Asturias se llevan las estadísticas. Ejemplo: la tabla por parroquias.

### Lo esencial

- Primero el pliego, después el código.
- Un cálculo, tres niveles de resultado: edificios, tramos de línea y territorio.
- Todo lo que depende del lugar se da como entrada, no se escribe en el código.
- Cada resultado tiene que tener un usuario.
- En lo que se publique, solo totales por territorio.

## Preguntas para un ingeniero

1. **¿Por qué no escribimos la velocidad del camión de bomberos directamente en el código, si para Lena ya la conocemos?**

:::{dropdown} Respuesta
En otro municipio la velocidad puede ser otra. Si está escrita en el código, la herramienta calculará en silencio un tiempo equivocado y no mostrará ningún error. Si la velocidad es una entrada, se ve y se cambia fácilmente.
:::

2. **La distribuidora pide una sola lista de casas ordenada por distancia a la línea. ¿Qué le faltará?**

:::{dropdown} Respuesta
La distribuidora planifica su trabajo por tramos de línea, no por casas. Y necesita el tiempo de viaje: una casa junto a una línea cerca de un parque de bomberos es menos urgente que la misma casa a una hora de camino. Por eso, para la distribuidora hacemos una capa de tramos con su clase de urgencia.
:::

3. **¿Para qué sirve el informe de ejecución, si el resultado ya está en nuestro disco?**

:::{dropdown} Respuesta
Los datos cambian: el Catastro se actualiza y los servicios cambian de dirección. Dentro de seis meses, la misma ejecución puede dar otras cifras. El informe muestra con qué datos y con qué versión de la herramienta calculamos. Sin él, el resultado no se puede ni repetir ni comprobar.
:::

## Ejercicios

1. **Recordar.** Nombra los tres usuarios de la herramienta. [Solución](#es-sol-1-1-1)

2. **Completar.** Escribe las palabras que faltan: «Todo lo que depende del lugar se lo damos ______, no lo escribimos ______». [Solución](#es-sol-1-1-2)

3. **Recordar el prólogo.** ¿Qué dos cosas pasaban en los dos partes de 2018? [Solución](#es-sol-1-1-3)

## La pregunta abierta

Hemos escrito muchas veces «cerca de la línea». Pero 20 metros de una línea de 20 kV y 20 metros de una de 400 kV, ¿son lo mismo o no? ¿Y desde dónde se mide la distancia? Con esto empieza la lección 1.2.

## Notas y fuentes

- [Dirección General del Catastro, edificios INSPIRE](https://www.catastro.hacienda.gob.es/webinspire/index.html): edificios con su uso, su número de viviendas y su año de construcción. Navarra y el País Vasco tienen sus propios catastros.
- [SITPA-IDEAS, Gobierno del Principado de Asturias: unidades administrativas](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/layers): capa de parroquias con datos de SADEI.
