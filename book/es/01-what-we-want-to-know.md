---
title: "Capítulo 1. Qué queremos saber exactamente"
---

## Dónde lo dejamos

El problema está claro: los árboles caen sobre los cables y sobre las carreteras, y en los concejos de montaña de Asturias eso se repite cada invierno. No encontré ninguna herramienta abierta que mostrara dónde vive gente junto a las líneas y cuánto tardan los bomberos en llegar hasta ella.

Así que hay que construirla. La calcularé con el ejemplo del concejo de Lena y la comprobaré en Cabrales. Pero antes de abrir QGIS quiero saber con exactitud qué tiene que devolver. Si no, puedo calcularlo todo bien y, aun así, estar respondiendo a otra pregunta.

## Para quién es la herramienta

Empiezo por quienes la van a usar. Son tres, y cada uno necesita algo distinto.

1. **La distribuidora.** Es la responsable de las líneas: se encarga de la poda y la tala a lo largo de los trazados, planifica las revisiones y decide adónde mandar una brigada antes de una borrasca. Necesita una lista de **tramos de línea** con más gente viviendo al lado y a los que más se tarda en llegar. Planifica el trabajo por líneas, no por casas.

2. **Los bomberos y el 112.** Cuando cae un cable y prende el monte, el 112 activa a los bomberos, que son los primeros en llegar. Necesitan saber de antemano **qué casas están junto a las líneas y cuántos minutos se tarda en llegar a ellas**, de día y de noche.

3. **El ayuntamiento.** Elabora el plan de emergencia municipal y concede las licencias de obra. Necesita **una visión de todo el territorio**: en qué partes del concejo hay viviendas junto a las líneas y lejos de los bomberos.

**De ahí sale el requisito principal: un solo cálculo y tres niveles de resultado: edificios, tramos de línea y territorio.**

## Qué van a recibir

Le das el límite de un concejo y la herramienta devuelve:

1. **Una capa de edificios residenciales.** Para cada edificio:
   - la distancia a la línea aérea más cercana y su tensión;
   - el número de viviendas, es decir, de pisos o casas;
   - el año de construcción: con él, el ayuntamiento ve si se sigue construyendo junto a las líneas;
   - el tiempo de desplazamiento de los bomberos, de día y de noche;
   - una clase de prioridad del 1 al 5.
2. **Una capa de tramos de línea.** Para cada tramo: cuántas viviendas hay cerca, cuánto se tarda en llegar a la más alejada, en minutos, y su clase de prioridad.
3. **Tablas por parroquias**, es decir, por las partes en que se divide el concejo: cuántos edificios y viviendas hay en cada clase.
4. **Un mapa de zonas de prioridad** sin casas individuales, para publicarlo y para el ayuntamiento.
5. **Un mapa de completitud de los datos de líneas:** dónde son fiables los datos abiertos y dónde tienen lagunas.
6. **Un informe de la ejecución:** qué datos de entrada se usaron y qué versión de la herramienta hizo el cálculo. Sin eso, el cálculo no se puede repetir y, por tanto, tampoco comprobar.

Todo se guarda en GeoPackage y CSV, para que cualquiera de los tres pueda abrir el resultado en su SIG.

Y una cosa desde el principio sobre los datos de las personas. La capa con cada edificio es de trabajo: la tiene quien ejecuta la herramienta, ya sea la distribuidora, los bomberos o el ayuntamiento. En los mapas y tablas que se publiquen solo habrá totales por territorio, y las cifras con muy pocas viviendas se ocultan, porque permitirían saber de qué casa se trata.

## Qué significa «cerca de una línea»

Aquí viene el primer matiz. Veinte metros hasta una línea de 20 kV y veinte metros hasta una de 400 kV no son lo mismo.

La norma española para líneas de alta tensión, la ITC-LAT 07, fija una distancia mínima entre los conductores y los edificios, y cuanto mayor es la tensión, mayor es esa distancia. El cumplimiento de la norma no se puede comprobar con datos abiertos: habla del conductor, que cuelga entre apoyos y se mueve con el viento, y nosotros solo tenemos el eje de la línea en el mapa. Por eso, de la norma tomo el ancho de una **franja de seguridad** alrededor del eje de la línea: una aproximación a la servidumbre de vuelo más la distancia de seguridad que fija la ITC-LAT 07. Los valores exactos los veremos en el capítulo sobre la tensión.

La distancia la mido desde el contorno del edificio, no desde su centro: en un edificio grande, la diferencia puede ser de decenas de metros. Para cada edificio guardo tres valores:

1. **La distancia al eje de la línea.** Es una medida.
2. **El semiancho de la franja** para la tensión de esa línea. Sale de la norma.
3. **La distancia al borde de la franja**, que es la primera menos la segunda. Si el edificio está dentro de la franja, sale negativa: indica cuántos metros ha entrado la casa más allá del borde.

Para comparar edificios junto a líneas distintas uso el tercer valor. La línea más cercana a un edificio es aquella cuyo borde de franja le queda más cerca.

Para las tablas, la distancia se divide en **intervalos de distancia**: por ejemplo, «dentro de la franja», «a menos de 25 m del borde», «25-50 m» y así sucesivamente. Dividir solo en «dentro» y «fuera» se queda corto: una casa a un metro del borde y otra a cuarenta acabarían en el mismo grupo. Los límites de los intervalos los elegiré cuando vea los datos.

En los datos abiertos, el eje de la línea está desplazado respecto al real. Cuánto, lo mediré en el capítulo sobre las líneas. **Un edificio en un intervalo cercano no es un incumplimiento de la norma, sino un candidato a revisión sobre el terreno.**

Solo tengo en cuenta las líneas de más de 1 kV, que para la norma española ya son de alta tensión. Sobre las de baja tensión de los pueblos casi no hay datos abiertos, y no voy a hacer como si estuvieran completos. Si la tensión de una línea no figura en los datos, tomo la franja más ancha posible para ese tipo de línea y la marco en el mapa de datos: es mejor revisar una casa de más que pasar por alto la que importa.

**Así que necesito la tensión de cada línea y una tabla de semianchos según la norma.**

## Cuánta gente vive cerca

Cuántas personas viven en cada edificio no lo dicen los datos abiertos. Lo más parecido es **el número de viviendas** del Catastro.

No es una medida exacta, y hay que saber hacia qué lado se equivoca:
- en los pueblos con muchas casas vacías o de temporada, **sobrestima** el número de habitantes;
- en residencias de mayores y hoteles, lo **subestima**.

El censo del INE indica qué parte de las viviendas de cada zona son viviendas principales. Con ese dato calcularé cuánto sobrestima en los pueblos de Lena.

**El número de viviendas es una aproximación, y voy a saber cuánto se desvía.**

## Cuánto tardan los bomberos

Hacen falta dos cosas: desde dónde salen y por dónde van.

1. **Los parques de bomberos.** No encontré ninguna capa abierta, así que haré yo misma la tabla: dónde está cada parque, qué concejos cubre y si está operativo las 24 horas. Las salidas las asigna el 112 por zonas, así que el parque más cercano en el mapa no es necesariamente el que va a salir.
2. **Las carreteras.** Las tomo de OpenStreetMap. La velocidad la fijo para cada tipo de vía, y para un camión de bomberos, no para un turismo: en una carretera de montaña con curvas, la diferencia es grande. Cada edificio lo conecto a la vía transitable más cercana. Si está demasiado lejos, o si solo se llega por una pista, el edificio queda marcado aparte.

De noche, el tiempo puede cambiar si algún parque no está operativo y la dotación tiene que salir de otro más lejano. Todavía no sé si los parques de la zona de Lena funcionan las 24 horas. Lo averiguaré cuando llegue a los parques de bomberos. Si funcionan todos, el tiempo de noche coincidirá con el de día, y eso también es una respuesta.

Los parques de bomberos son los puntos de salida por defecto. La compañía puede poner en su lugar las bases de sus brigadas, y el mismo cálculo le dirá cuánto tardan sus equipos en llegar a los edificios de cada tramo.

Y lo aclaro ya: **solo calculo el tiempo de desplazamiento con las carreteras despejadas.** No incluye el tiempo de gestión de la llamada en el 112 ni el de activación de la dotación, y tampoco la nieve ni los árboles caídos. El tiempo real siempre es mayor, y en una borrasca, más que nunca.

Otra trampa es el límite del concejo. El parque que cubre Lena puede estar en el concejo de al lado, y el camino más rápido puede pasar por él. Si recorto las carreteras justo por el límite, el tiempo saldrá mal y el programa no avisará de nada.

**Por eso, los edificios y las líneas los tomo dentro del concejo, pero las carreteras y los parques, con un margen de varios kilómetros alrededor.**

## Cómo decidir por dónde empezar

Ahora cada edificio tiene una distancia y un tiempo. A la compañía eso no le basta: necesita saber por dónde empezar.

Para eso uso una **matriz de prioridad**. En un eje, el intervalo de distancia; en el otro, el intervalo de tiempo de desplazamiento; en el cruce, una clase del 1 (revisar primero) al 5. Por ejemplo:

| | hasta 15 min | 15-30 min | más de 30 min |
|---|---|---|---|
| **dentro de la franja** | 2 | 1 | 1 |
| **a menos de 25 m del borde** | 3 | 2 | 1 |
| **25-50 m** | 4 | 3 | 2 |
| **a más de 50 m** | 5 | 5 | 4 |

Los números son solo un ejemplo. Los intervalos y las clases reales los elegiré en el capítulo sobre la prioridad.

Ojo: esto no es la probabilidad de una avería. La matriz indica otra cosa: lo grave que sería si ocurriera. ¿Y por qué una matriz y no una fórmula con pesos? Porque con una fórmula no se ve de dónde sale el resultado. Con la matriz, un ingeniero ve enseguida por qué un tramo tiene la clase 1: por ejemplo, hay casas dentro de la franja y se tarda más de 30 minutos en llegar. La matriz se guarda en una tabla aparte, así que la compañía puede adaptarla a sus propias reglas sin tocar el código.

Los tramos de línea los construyo así:
1. **Donde los datos incluyen los apoyos**, el tramo es el vano entre dos de ellos: así la compañía lo encuentra en su propio inventario. Donde no hay apoyos, la línea se corta en tramos de una longitud fija.
2. **Cada edificio se asigna a un solo tramo**, el más cercano según la misma regla, hasta el borde de la franja.
3. **La clase del tramo** es la más urgente de las de sus edificios, es decir, la de número más bajo. Dentro de una misma clase, los tramos se ordenan por número de viviendas.

**La compañía recibe una lista ordenada de tramos, y cada puesto de la lista se puede justificar.**

## ¿Podemos fiarnos del resultado?

Un resultado que no se puede comprobar no le sirve a la compañía. Por eso, la comprobación forma parte de cada paso:

1. **Líneas de dos fuentes.** Comparo la Base Topográfica Nacional (BTN) con OpenStreetMap. Donde no coinciden, los datos son menos fiables, y eso aparecerá en el mapa de datos.
2. **A ojo, sobre la ortofoto.** Reviso dos muestras sobre la ortofoto del PNOA. La primera, edificios de los intervalos cercanos: ¿de verdad hay una casa y una línea al lado? La segunda, edificios lejos de todas las líneas conocidas: ¿no habrá cerca una línea que no figure en ninguna de las dos fuentes?
3. **No se ha perdido nada.** La suma de viviendas por parroquias tiene que coincidir con la suma por edificios. Si no coincide, algunos edificios se han quedado fuera entre los límites de las parroquias, o se han contado dos veces.
4. **El tiempo, a mano.** Para unos cuantos edificios trazo yo misma la ruta y la comparo con el cálculo.
5. **Estabilidad.** Repito el cálculo de prioridades con otros límites de intervalo y otras velocidades. Los tramos que siguen en la clase 1 con cualquier valor razonable son las conclusiones más sólidas.
6. **Otro concejo.** La misma herramienta, sin cambiar una sola línea de código, calcula Cabrales. Si para Cabrales hay que tocar el código, es que en algún sitio hay algo escondido que solo vale para Lena.

**El error más peligroso es el que no da error.** Si en los datos falta una línea que en realidad existe, las casas que tiene al lado parecerán «a salvo» y el programa no se dará cuenta. Por eso compruebo la completitud de los datos de líneas antes de calcular distancias con ellos.

## Qué datos hacen falta

| Qué | Para qué | Dónde se obtiene en España |
|---|---|---|
| Límite del concejo | Acotar edificios y líneas | Instituto Geográfico Nacional (IGN) |
| Líneas aéreas y apoyos | Fuente de peligro, tramos | BTN (IGN) y OpenStreetMap |
| Tensión de las líneas | Ancho de la franja de seguridad | OpenStreetMap |
| Edificios residenciales | Dónde vive gente y cuánta | Catastro, conjunto de edificios INSPIRE |
| Parques de bomberos | Desde dónde sale la ayuda | Tabla elaborada por mí |
| Carreteras | Camino de la ayuda | OpenStreetMap |
| Parroquias | Totales por territorio | Servicio cartográfico del Principado de Asturias |
| Censo | Parte de viviendas principales | INE |
| Ortofoto | Comprobación a ojo | PNOA |

Todos estos datos son abiertos y gratuitos. Si están completos o no, lo veré sobre la marcha.

:::{admonition} Tus datos
OpenStreetMap tiene líneas, apoyos y carreteras de cualquier país, pero en las líneas pequeñas a menudo falta la tensión: es lo primero que tienes que comprobar. No todos los países publican los edificios con el número de viviendas. En España están en el Catastro, salvo en Navarra y el País Vasco, que tienen catastro propio. Casi en todas partes tendrás que hacerte tú la tabla de parques. Usa la norma de distancias de tu país: en la tabla de semianchos basta con cambiar los valores. Y elige un sistema de coordenadas métrico para tu zona; para Asturias es el EPSG:25830.
:::

## En qué orden trabajar

El orden lo marcan los propios datos: la distancia no se puede calcular sin líneas y edificios, el tiempo no se puede calcular sin parques y carreteras, y la prioridad necesita las dos cosas. Cada etapa es un capítulo.

**Preparación**

2. Python en QGIS: el primer script
3. Límite del concejo y sistema de coordenadas

**Datos de partida**

4. Líneas aéreas de dos fuentes
5. Tensión de las líneas y franja de seguridad
6. ¿Faltan líneas? Completitud de los datos
7. Edificios residenciales del Catastro

**Quién vive junto a las líneas**

8. Distancia de los edificios a las líneas
9. Viviendas junto a las líneas por parroquias

**Cuánto tarda en llegar la ayuda**

10. Parques de bomberos y zonas de salida
11. Las carreteras como red para calcular rutas
12. Tiempo de desplazamiento de día y de noche

**Qué revisar primero**

13. Prioridad de edificios y tramos de línea
14. Estabilidad del resultado

**La herramienta terminada**

15. Del script al algoritmo de Processing
16. Prueba en otro concejo: Cabrales
17. Mapas e informe para la compañía
18. Complemento de QGIS y versión 1.0

Al final no tendremos un script, sino un **complemento de QGIS** (plugin): se instala desde un archivo zip y nuestro algoritmo aparece en la Caja de herramientas de Procesos. Así es como se instalan las herramientas en las empresas.

## Qué no hace la herramienta

No evalúa **dónde es más probable una avería**. Para eso habría que saber qué bosque hay a lo largo del trazado y cómo cae en una borrasca, y eso es otro trabajo, y grande. La herramienta responde a otra pregunta: si hay una avería, quién está cerca y cuánto se tarda en llegar hasta él.

Los demás límites, como el cumplimiento normativo, los cortes de suministro y los campos electromagnéticos, ya los expliqué en el prólogo y no cambian.

## Resumen

**Conceptos nuevos**
- **Línea aérea:** línea cuyos conductores cuelgan de apoyos.
- **Vivienda:** piso o casa unifamiliar.
- **Franja de seguridad:** banda alrededor del eje de la línea cuyo ancho depende de la tensión.
- **Intervalo de distancia:** rango de distancias entre el edificio y el borde de la franja, por ejemplo, 25-50 m.
- **Tiempo de desplazamiento:** lo que se tarda en ir desde el parque de bomberos hasta el edificio por carreteras despejadas.
- **Matriz de prioridad:** tabla en la que el cruce de un intervalo de distancia y un intervalo de tiempo da una clase del 1 al 5.

**Lo esencial**
- La distancia se compara con el borde de la franja de seguridad, porque el ancho de la franja depende de la tensión.
- El error más peligroso es una línea que falta: el programa no lo detecta, así que compruebo los datos antes de calcular.

## Preguntas para un ingeniero

1. Un edificio está a 30 m del eje de una línea de 20 kV y a 35 m del eje de una de 400 kV. ¿Cuál es su línea más cercana?

:::{dropdown} Respuesta
Aquella cuyo borde de franja le queda más cerca. La franja de la línea de 400 kV es más ancha, así que su borde puede estar más cerca aunque el eje esté más lejos. Si midiera hasta el eje, podría asignar el edificio a la línea de menor tensión.
:::

2. ¿Por qué no basta con revisar en la ortofoto solo los edificios que están junto a las líneas?

:::{dropdown} Respuesta
Porque esa muestra ya parte de las líneas que figuran en los datos. Si una línea no está en ninguna fuente, los edificios que tiene al lado no entran en la muestra y la revisión no la encontrará. Una línea que falta solo se puede descubrir mirando también donde, según los datos, no hay líneas.
:::

3. ¿Por qué la clase de un tramo es la más urgente de las de sus edificios y no la media?

:::{dropdown} Respuesta
Porque una sola casa dentro de la franja a la que se tarda 40 minutos en llegar ya es motivo para revisar el tramo, por muchas casas lejos de la línea que haya en el tramo. La media diluiría esa casa entre las demás, y el tramo parecería más seguro de lo que es.
:::

## La pregunta que queda abierta

Tenemos el plan, sabemos qué datos hacen falta y en qué orden trabajar. Toca abrir QGIS. ¿Dónde está Python dentro de él y cómo se le da la primera orden?
