---
title: "Lección 1.2. Cómo calculamos y cómo lo comprobamos"
---

## Dónde lo dejamos

En la lección 1.1 decidimos para quién construimos la herramienta, qué le damos como entrada y qué devuelve. Pero en el pliego quedaron palabras que todavía no hemos definido: «cerca de la línea», «cuánta gente», «cuánto se tarda», «qué revisar primero». De momento son solo palabras. Un ordenador no puede calcularlas.

## Qué queremos entender

La pregunta de la lección: **¿cómo convertimos cada una de esas palabras en algo que se pueda calcular, y cómo comprobamos después que podemos fiarnos del resultado?**

¿Cómo sabremos si nos hemos equivocado? Si de alguna palabra no podemos decir con qué datos y de qué manera la obtendremos, el pliego todavía no está listo.

Vamos palabra por palabra.

## Paso 1. Qué significa «cerca de la línea»

Empezamos por lo sencillo. Una casa a 20 metros de una línea de 20 kV y otra a 20 metros de una de 400 kV, ¿están igual de cerca?

No. Cuanto mayor es la tensión, más lejos de los conductores tienen que estar los edificios. Lo dice la norma ITC-LAT 07: fija una distancia de seguridad entre los conductores y los edificios, distinta para cada tensión[^1]. Alrededor de la línea queda una franja en la que no debe haber edificios. La llamamos **franja de seguridad**.

Ahora, una pregunta más difícil: ¿desde dónde se mide esa franja? La norma mide la distancia desde el **conductor en su máxima desviación por el viento**, no desde el centro de la línea[^2]. Los conductores cuelgan a los lados del apoyo, y con viento se desplazan todavía más.

¿Y qué tenemos nosotros? En el mapa, la línea es solo eso, una línea: el eje. No conocemos ni la separación entre conductores ni cuánto los desplaza el viento. Así que, si trazamos la franja desde el eje, nos sale **más estrecha que la real**. No pasa nada, siempre que lo digamos claramente: nuestra franja es una **estimación por defecto**. La franja real es más ancha, y puede haber más casas dentro.

Entonces, ¿cómo comparamos casas junto a líneas de distinta tensión? Para cada edificio guardamos tres números:

1. **La distancia al eje de la línea:** esta la medimos.
2. **La semianchura de la franja de seguridad** para la tensión de esa línea: la sacamos de la norma.
3. **La distancia al borde de la franja:** el primer número menos el segundo. Si la casa está dentro de la franja, el número es negativo.

Comparamos los edificios por el tercer número. Así una casa junto a una línea de 20 kV y otra junto a una de 400 kV se pueden comparar de forma justa. La distancia se mide desde la fachada del edificio, no desde su centro: el peligro empieza en la fachada.

**Un detalle más.** Dividir las casas solo en «dentro de la franja» y «fuera» es demasiado grueso. Una casa a un metro del borde de la franja y otra a cuarenta acabarían en el mismo grupo. Por eso dividimos la distancia en varios **intervalos**: dentro de la franja y, más allá, varios escalones. Dónde poner los límites de los escalones lo decidiremos cuando veamos los datos.

**Qué líneas tomamos.** Solo las de alta tensión. Según el reglamento español, todo lo que supera 1 kV[^3]. ¿Y si en los datos una línea no tiene la tensión indicada? Entonces le asignamos la franja más ancha y la marcamos en el mapa de completitud. Más vale revisar una casa de más que dejarse una que hacía falta.

**Así que una casa en el intervalo más cercano no es un incumplimiento de la norma, sino un sitio que conviene revisar.**

## Paso 2. Cuánta gente vive cerca

Los datos abiertos no dicen cuántas personas viven en cada casa. ¿Qué es lo más parecido que tenemos? En el Catastro, cada edificio tiene indicado su **número de viviendas**, es decir, de pisos o casas independientes[^4].

Pero una vivienda no es una persona. Pensemos hacia qué lado nos equivocaremos:

- en los pueblos con muchas casas vacías o de veraneo, en realidad vive **menos** gente que viviendas hay;
- en las residencias de mayores y en los hoteles hay **más** personas.

Por eso no nos fiamos del Catastro a ciegas, sino que lo comprobamos con otras dos fuentes independientes:

1. **La capa de parroquias de Asturias,** que da el número de viviendas de cada parroquia[^5].
2. **La malla europea de población** del censo de 2021, con celdas de 1 km[^6].

Si las tres fuentes coinciden más o menos, podemos fiarnos del Catastro. Si no, averiguamos por qué.

## Paso 3. Cuánto tarda en llegar la ayuda

Para calcular el tiempo de viaje hacen falta dos cosas: desde dónde se sale y por qué carreteras se va.

1. **Desde dónde se sale.** En Asturias hay 19 parques de bomberos del SEPA, el Servicio de Emergencias del Principado de Asturias[^7]. No encontramos ninguna capa ya hecha con sus coordenadas y sus horarios, así que haremos la tabla nosotros. De noche solo pueden salir los parques que tienen guardia de 24 horas. De ahí lo de «de día y de noche» en nuestra pregunta. Si alrededor de Lena todos los parques tienen guardia de 24 horas, el tiempo de día y el de noche coincidirán, y eso también es una respuesta.
2. **Por qué carreteras se va.** La velocidad la fijamos para un camión de bomberos, no para un turismo: en una carretera de montaña llena de curvas la diferencia es grande. Cada casa la conectamos con la carretera más cercana por la que se pueda circular.

**Aquí hay una trampa con el límite.** El parque de bomberos que sale hacia Lena puede estar en un municipio vecino. Y la carretera más rápida también puede pasar por los vecinos. Si recortamos las carreteras justo por el límite de Lena, el tiempo saldrá mal y el programa no dará ni un solo error.

**Por eso las casas y las líneas las tomamos dentro del municipio, y las carreteras y los parques de bomberos, con un margen alrededor.**

Y una limitación importante. Solo calculamos el tiempo en carretera, con las carreteras despejadas. Sin la recepción del aviso, sin el tiempo de salida, sin nieve y sin árboles caídos. El tiempo real siempre es mayor, y en un temporal mucho más. **Nuestro tiempo de viaje es un límite inferior:** más rápido que eso, seguro que la ayuda no llega.

## Paso 4. Por dónde empezar la revisión

Supongamos que cada casa ya tiene su distancia y su tiempo de viaje. La distribuidora tiene que decidir por dónde empezar. ¿Cómo sacamos una sola decisión de dos números?

Podríamos inventar una fórmula con pesos: tanto por la distancia, tanto por el tiempo. Pero en una fórmula no se ve por qué un tramo ha quedado el primero. Por eso lo hacemos más sencillo: una tabla, la **matriz de urgencia**:

- en un eje, el intervalo de distancia: cerca, medio, lejos;
- en el otro, el intervalo de tiempo de viaje: rápido, medio, largo;
- en el cruce, una clase del 1 (revisar primero) al 5.

En una tabla así, el ingeniero ve enseguida por qué un tramo tiene la clase 1: por ejemplo, porque hay casas dentro de la franja y se tarda mucho en llegar. La tabla se guarda en un archivo aparte. La distribuidora puede adaptarla sin tocar el código.

Y lo aclaramos ya: la matriz no es una probabilidad de avería. Muestra lo grave que será **si** la avería se produce.

## Paso 5. Cómo vamos a comprobar el resultado

Un resultado que no se puede comprobar no le sirve a la distribuidora. Así que tenemos que saber de antemano cómo lo vamos a comprobar.

Aquí la palabra clave es «de antemano». La tentación es fuerte: calcular primero y luego elegir una comprobación que el resultado supere. Para que eso no pase, las reglas de comprobación las escribimos **ahora**, antes del primer cálculo, y las guardamos en el repositorio del proyecto. La fecha en que se guardan mostrará que las reglas existían antes que el resultado.

| Qué comprobamos | Cómo lo comprobamos | Qué consideramos un fallo |
|---|---|---|
| Distancias | muestra aleatoria de edificios cerca de las líneas y lejos de ellas, a ojo sobre la ortofoto | en la ortofoto la casa está claramente en otro intervalo que en el cálculo |
| Sumas | suma de viviendas por parroquias frente a la suma por edificios | las sumas no coinciden exactamente |
| Viviendas | Catastro frente a la capa de parroquias y la malla de población | grandes diferencias que no sabemos explicar |
| Tiempo de viaje | varias rutas a mano y una fórmula independiente para camiones de bomberos | un tiempo de otro orden de magnitud |
| Portabilidad | la misma herramienta, sin tocar ni una línea de código, en otro municipio: Cabrales | hay que cambiar el código |
| Robustez | nuevo cálculo con otros límites de intervalos y otras velocidades | la lista de los primeros tramos cambia por completo |

**Cualquier resultado de una comprobación es un resultado del proyecto, aunque no nos guste.**

## Lo que la herramienta no hace

También lo dejamos escrito claramente, para no prometer después de más:

- no comprueba si se cumple la norma: para eso hace falta la flecha real del conductor;
- no calcula quién se queda sin luz: para eso hace falta el esquema de la red, y no es público;
- no evalúa los campos electromagnéticos;
- no afirma que todas las líneas de los datos sean aéreas: una parte puede estar soterrada.

## Qué ha salido

Lo comparamos con la pregunta de la lección. Ahora cada palabra del pliego se puede calcular:

| Palabra | Cómo la calculamos |
|---|---|
| «cerca de la línea» | distancia al borde de la franja de seguridad según la norma, por intervalos |
| «cuánta gente» | número de viviendas del Catastro, con dos comprobaciones |
| «cuánto se tarda» | tiempo de viaje del camión de bomberos con las carreteras despejadas: un límite inferior |
| «qué revisar primero» | clase de urgencia según la matriz |

Y tenemos las reglas de comprobación, escritas antes del cálculo. El pliego de condiciones está listo.

De él sale el orden de trabajo. Cada capítulo es una etapa:

2. Introducción a Python y a PyQGIS.
3. El límite del municipio y el primer script.
4. Las líneas eléctricas.
5. La tensión y la franja de seguridad.
6. ¿Podemos fiarnos de los datos de líneas?
7. Los edificios residenciales.
8. Quién vive junto a las líneas.
9. Cuánto tarda en llegar la ayuda.
10. Por dónde empezar la revisión.
11. Los rásteres.
12. Del script a la herramienta.
13. La comprobación de la herramienta.
14. El resultado para la distribuidora: un plugin de QGIS.

## Tus datos

Con datos propios, normalmente hay que cambiar:

- las velocidades por tipo de carretera, según el relieve;
- los límites de los intervalos de distancia y de tiempo;
- la matriz de urgencia, si la distribuidora tiene sus propias reglas sobre qué revisar primero.

Todo esto son entradas: no hace falta tocar el código.

## Resumen

### Conceptos nuevos

- **Franja de seguridad:** la zona alrededor de la línea en la que, según la norma, no debe haber edificios. Ejemplo: nosotros la trazamos desde el eje de la línea, así que es una estimación por defecto.
- **Distancia al borde de la franja:** la distancia al eje menos la semianchura de la franja. Ejemplo: un número negativo significa que la casa está dentro de la franja.
- **Intervalo de distancia:** un escalón de distancia desde el borde de la franja. Ejemplo: «dentro de la franja».
- **Límite inferior:** el valor por debajo del cual no puede estar la magnitud real. Ejemplo: nuestro tiempo de viaje.
- **Matriz de urgencia:** la tabla «intervalo de distancia × intervalo de tiempo → clase». Ejemplo: casa dentro de la franja y mucho tiempo de viaje, clase 1.

### Lo esencial

- Cada palabra del pliego hay que convertirla en algo que se pueda calcular.
- Las casas junto a líneas de distinta tensión se comparan por la distancia al borde de la franja.
- La franja desde el eje y el tiempo con carreteras despejadas son estimaciones que pintan las cosas mejor de lo que son. Lo decimos claramente.
- Las carreteras y los parques de bomberos se toman con un margen más allá del límite del municipio.
- Las reglas de comprobación se escriben antes del cálculo.

## Preguntas para un ingeniero

1. **¿Por qué comparamos las casas por la distancia al borde de la franja y no simplemente por la distancia a la línea?**

:::{dropdown} Respuesta
Las líneas de distinta tensión tienen franjas de seguridad de distinta anchura. A 20 metros de una línea de 20 kV podemos estar ya fuera de la franja, y a 20 metros de una de 400 kV, todavía dentro. La distancia al borde de la franja tiene en cuenta la tensión, y así las casas se pueden comparar.
:::

2. **¿Por qué no recortamos las carreteras justo por el límite del municipio?**

:::{dropdown} Respuesta
El parque de bomberos más cercano y la carretera más rápida pueden quedar al otro lado del límite. Una red de carreteras recortada dará un tiempo equivocado, y el programa no mostrará ni un solo error.
:::

3. **¿Por qué las reglas de comprobación se guardan en el repositorio antes del cálculo?**

:::{dropdown} Respuesta
La fecha en que se guardan demuestra que las reglas existían antes que el resultado. Así nos protegemos de la tentación de ajustar la comprobación a lo que ha salido.
:::

## Ejercicios

1. **Recordar.** ¿Qué tres números guardamos para cada edificio para poder comparar casas junto a líneas de distinta tensión? [Solución](#es-sol-1-2-1)

2. **Completar.** Escribe las palabras que faltan: «Las casas y las líneas las tomamos dentro del municipio, y las carreteras y los parques de bomberos, ______». [Solución](#es-sol-1-2-2)

3. **Recordar la lección 1.1.** ¿Por qué todo lo que depende del lugar se lo damos a la herramienta como entrada? [Solución](#es-sol-1-2-3)

## La pregunta abierta

El pliego está listo, y antes que nada cualquier cálculo necesita un territorio: el límite del municipio. ¿Cómo lo obtenemos en QGIS con código y no con el ratón? ¿Y qué es, en realidad, ese código, de qué está hecho? Con esto empieza el capítulo 2: la introducción a Python y a PyQGIS.
---

## Notas y fuentes

[^1]: [BOE, Real Decreto 223/2008, ITC-LAT 07, apartado 5.12.2](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf). Consultado el 19/11/2026.
[^2]: Ibídem: la distancia Dadd + Del = 3,3 + Del, con un mínimo de 5 m, se mide desde los conductores en su máxima desviación. Véase también [J. Andreu, «Distancias eléctricas de seguridad en LAAT», FEMPA, 2016](https://www.fempa.es/wp-content/uploads/oldfiles/documentos/20160203111250_Jornada%20distancias%20electricas%20020216_Jorge%20Andreu.pdf). Consultado el 19/11/2026.
[^3]: [BOE, Real Decreto 223/2008, ITC-LAT 01](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf): se considera alta tensión toda tensión nominal superior a 1 kV. Consultado el 19/11/2026.
[^4]: [Dirección General del Catastro, edificios INSPIRE](https://www.catastro.hacienda.gob.es/webinspire/index.html): atributos currentUse, numberOfDwellings y dateOfConstruction; véase también [IDEE, JIIDE 2017](https://www.idee.es/resources/presentaciones/JIIDE17/Apresentacoes_JIIDE2017_Conjunto_datos_edificios_BU_en_la_Direccion_General_del_Catastro_Pablo_Gomez_Dominguez.pdf). Consultado el 19/11/2026.
[^5]: [SITPA-IDEAS, Gobierno del Principado de Asturias: unidades administrativas](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/layers): capa de parroquias con datos de SADEI. Consultado el 19/11/2026.
[^6]: [Eurostat GISCO, malla de población GEOSTAT 2021](https://ec.europa.eu/eurostat/en/web/gisco/geodata/population-distribution/population-grids). Consultado el 19/11/2026.
[^7]: [112 Asturias, red de parques de bomberos del SEPA](https://www.112asturias.es/parques-bomberos). Consultado el 19/11/2026.
