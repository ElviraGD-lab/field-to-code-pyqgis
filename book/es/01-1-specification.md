---
title: "Lección 1.1. El pliego de condiciones"
---

## Dónde lo dejamos

En el prólogo vimos el mismo cuadro en dos partes de 2018: el temporal rompe las líneas, los árboles y la nieve cortan las carreteras y la ayuda no puede llegar. No encontramos ninguna herramienta abierta que, para cualquier municipio, muestre qué casas están cerca de líneas aéreas y cuánto tarda en llegar la ayuda hasta ellas.

Así que la construimos nosotros. Pero si abrimos QGIS y nos ponemos a calcular sin más, es fácil hacer bien todas las cuentas y aun así responder a otra pregunta. Por eso el primer paso es sin código.

## Qué queremos entender en esta lección

La pregunta principal de la lección: **¿qué tiene que darnos la herramienta para que con su resultado se pueda tomar una decisión?**

Para responder, tenemos que decidir cinco cosas:

- quién va a usar la herramienta;
- qué hay que darle de entrada;
- qué devuelve como resultado;
- qué supuestos hacemos;
- cómo vamos a comprobar el resultado.

¿Cómo sabremos si nos hemos equivocado? Si de algún resultado no podemos decir qué hará con él el usuario, ese resultado sobra. Y si al usuario le falta algo para decidir, es que se nos ha pasado.

Los ingenieros llaman a este documento **pliego de condiciones**. En el fondo es un acuerdo con nosotros mismos: qué construimos y cómo sabremos que lo hemos construido.

## Para quién es la herramienta

La herramienta tiene tres usuarios, y cada uno necesita algo distinto.

1. **La distribuidora.** Es la responsable de las líneas: hace la poda y tala a lo largo de los trazados, planifica las inspecciones y decide adónde manda una brigada antes de un temporal. Planifica su trabajo por líneas, no por casas. Necesita una lista de tramos de línea: dónde hay más viviendas cerca y adónde se tarda más en llegar.
2. **Los bomberos y el 112.** Si cae un conductor y empieza un incendio, casi siempre los primeros en llegar son los bomberos. Necesitan saber de antemano qué casas están junto a las líneas y cuántos minutos se tarda en llegar a ellas, de día y de noche.
3. **El ayuntamiento.** Redacta el plan de emergencias y concede las licencias de obra. Necesita ver todo el territorio: en qué partes del municipio hay viviendas junto a las líneas y lejos de los bomberos.

**Así que el cálculo es uno, pero el resultado hace falta en tres niveles: edificios, tramos de línea y territorio.**

## Qué entra y qué sale

**Entrada:**

- el límite del municipio;
- la capa de líneas eléctricas (por defecto, datos abiertos; la distribuidora puede tener la suya);
- la tabla de parques de bomberos;
- las velocidades por tipo de carretera;
- los límites de los intervalos de distancia y la tabla de prioridades.

Todo lo que depende del lugar se define en la entrada. En el código no puede quedar nada que solo valga para Lena.

**Salida:**

1. **Capa de edificios residenciales.** Para cada edificio:
   - la distancia a la línea más cercana y la tensión de esa línea;
   - el número de viviendas del edificio;
   - el año de construcción;
   - el tiempo de viaje de los bomberos, de día y de noche;
   - la clase de prioridad.
2. **Capa de tramos de línea:** cuántas viviendas hay cerca de cada tramo, cuánto se tarda en llegar a la casa más lejana y la clase de prioridad.
3. **Tabla por parroquias.** La parroquia es la división del municipio con la que en Asturias se llevan las estadísticas.
4. **Ráster de zonas de prioridad** en una malla regular, sin casas individuales.
5. **Mapa de completitud de los datos de líneas:** dónde podemos fiarnos de los datos abiertos y dónde tienen huecos.
6. **Informe de ejecución:** qué datos se dieron de entrada, de qué versión eran y qué versión de la herramienta hizo el cálculo. Sin él, el cálculo no se puede repetir y, por tanto, tampoco comprobar.

Todo se guarda en GeoPackage y CSV, para que cada usuario pueda abrir el resultado en su SIG o en una hoja de cálculo.

**Sobre los datos privados.** La capa con cada casa se queda en manos de quien ejecuta la herramienta. En lo que se publique solo habrá totales por territorio. Si detrás de una cifra hay solo unas pocas casas, la ocultamos.

## Qué significa «cerca de la línea»

Veinte metros de una línea de 20 kV y veinte metros de una de 400 kV no son lo mismo. La norma española para líneas aéreas de alta tensión, la ITC-LAT 07, fija una distancia de seguridad entre los conductores y los edificios: cuanto mayor es la tensión, mayor es la distancia[^1].

Aquí hay un detalle importante. La norma mide esa distancia desde el conductor en su máxima desviación por el viento, no desde el eje de la línea[^2]. Nosotros solo tenemos el eje de la línea en el mapa. No conocemos ni la separación entre conductores ni cuánto los desplaza el viento. Por eso la franja de seguridad que trazamos desde el eje nos sale más estrecha que la real. **Es una estimación por defecto, y la llamamos así.**

Para cada edificio guardamos tres números:

1. **La distancia al eje de la línea:** es nuestra medición.
2. **La semianchura de la franja de seguridad** para la tensión de esa línea: sale de la norma.
3. **La distancia al borde de la franja:** el primero menos el segundo. Si la casa está dentro de la franja, el número es negativo.

Comparamos los edificios por el tercer número. Así una casa junto a una línea de 20 kV y otra junto a una de 400 kV se pueden comparar de forma justa. La distancia se mide desde el contorno del edificio, no desde su centro.

Para las tablas, dividimos las distancias en intervalos: dentro de la franja y, más allá, en varios escalones. Dividir solo en «dentro» y «fuera» es demasiado grueso: una casa a un metro del borde de la franja y otra a cuarenta acabarían en el mismo grupo. Los límites de los intervalos los elegiremos cuando veamos los datos.

**Qué líneas tomamos.** Solo las de alta tensión: según el reglamento español, todo lo que supera 1 kV[^3]. Si en los datos una línea no tiene la tensión indicada, le asignamos la franja más ancha y la marcamos en el mapa de completitud. Más vale revisar una casa de más que dejarse una que hacía falta.

**Que una casa esté en el intervalo más cercano no es un incumplimiento de la norma, sino un sitio que conviene revisar.**

## Cuánta gente vive cerca

Los datos abiertos no dicen cuántas personas viven en cada casa. Lo más parecido es el número de viviendas del Catastro[^4].

No es exacto, y sabemos de antemano hacia qué lado se equivoca:

- en los pueblos con muchas casas vacías o de veraneo, en realidad vive menos gente;
- en las residencias de mayores y en los hoteles hay más personas que viviendas.

Por eso no nos fiamos del Catastro a ciegas, sino que lo comprobamos con dos fuentes independientes:

1. **La capa de parroquias de Asturias,** que da el número de viviendas de cada parroquia[^5].
2. **La malla europea de población** del censo de 2021, con celdas de 1 km[^6].

## Cuánto tarda en llegar la ayuda

Hacen falta dos cosas: desde dónde se sale y por qué carreteras se va.

1. **Parques de bomberos.** En Asturias, el SEPA, el Servicio de Emergencias del Principado de Asturias, tiene 19[^7]. No encontramos ninguna capa ya hecha con sus coordenadas y sus horarios, así que haremos la tabla nosotros. De noche solo pueden salir los parques que tienen guardia de 24 horas. Si alrededor de Lena todos los parques la tienen, el tiempo de día y el de noche coincidirán: eso también es una respuesta.
2. **Carreteras.** La velocidad la fijamos para un camión de bomberos, no para un turismo: en una carretera de montaña llena de curvas la diferencia es grande. Cada casa la conectamos con la carretera transitable más cercana.

**La trampa del límite.** El parque que sale hacia Lena puede estar en un municipio vecino, y la carretera más rápida también puede pasar por los vecinos. Si recortamos las carreteras justo por el límite, el tiempo saldrá mal y el programa no dará ni un solo error. Por eso las casas y las líneas las tomamos dentro del municipio, y las carreteras y los parques de bomberos, con un margen alrededor.

**Una limitación importante:** solo calculamos el tiempo de viaje con las carreteras despejadas. Sin la recepción del aviso, sin el tiempo de salida, sin nieve y sin árboles caídos. El tiempo real siempre es mayor, y en un temporal mucho más. **Nuestro tiempo es un límite inferior.**

## Por dónde empezar la revisión

Cuando cada casa tiene su distancia y su tiempo, la distribuidora necesita saber por dónde empezar. Para eso usamos una **matriz de prioridad**:

- en un eje, el intervalo de distancia: cerca, medio, lejos;
- en el otro, el intervalo de tiempo de viaje: rápido, medio, largo;
- en el cruce, una clase del 1 (revisar primero) al 5.

¿Por qué una tabla y no una fórmula con pesos? En una fórmula no se ve de dónde sale el resultado. En la tabla, el ingeniero ve enseguida por qué un tramo tiene la clase 1: por ejemplo, porque hay casas dentro de la franja y se tarda mucho en llegar. La tabla se guarda en un archivo aparte, y la distribuidora puede adaptarla sin tocar el código.

**La matriz no es una probabilidad de avería. Muestra lo grave que será si la avería se produce.**

## Cómo vamos a comprobar el resultado

Un resultado que no se puede comprobar no le sirve a la distribuidora. Por eso las reglas de comprobación las escribimos ahora, antes del primer cálculo, y las guardamos en el repositorio del proyecto. La fecha en que se guardan demostrará que las reglas existían antes que el resultado. Si no, la tentación de ajustar la comprobación a lo que ha salido es muy grande.

| Qué comprobamos | Cómo | Qué consideramos un fallo |
|---|---|---|
| Distancias | muestra aleatoria de edificios cerca de las líneas y lejos de ellas, a ojo sobre la ortofoto | en la ortofoto la casa está claramente en otro intervalo que en el cálculo |
| Sumas | suma de viviendas por parroquias frente a la suma por edificios | las sumas no coinciden exactamente |
| Viviendas | Catastro frente a la capa de parroquias y la malla de población | grandes diferencias que no sabemos explicar |
| Tiempo de viaje | varias rutas a mano y una fórmula independiente para camiones de bomberos | un tiempo de otro orden de magnitud |
| Portabilidad | la misma herramienta, sin tocar ni una línea de código, en Cabrales | hay que cambiar el código |
| Robustez | nuevo cálculo con otros límites de intervalos y otras velocidades | la lista de los primeros tramos cambia por completo |

**Cualquier resultado de una comprobación es un resultado del proyecto, aunque no nos guste.**

## Lo que la herramienta no hace

- No comprueba el cumplimiento de la norma: para eso hace falta la flecha real del conductor.
- No calcula quién se queda sin luz: para eso hace falta el esquema de la red, y no es público.
- No evalúa los campos electromagnéticos.
- No afirma que todas las líneas de los datos sean aéreas: una parte puede estar soterrada.

## Etapas del trabajo

Del pliego sale el orden de trabajo del proyecto. Cada etapa es un capítulo, a partir del segundo (el primero es este pliego):

2. El límite del municipio y el primer script.
3. Las líneas eléctricas.
4. La tensión y la franja de seguridad.
5. ¿Podemos fiarnos de los datos de líneas?
6. Los edificios residenciales.
7. Quién vive junto a las líneas.
8. Cuánto tarda en llegar la ayuda.
9. Por dónde empezar la revisión.
10. Los rásteres.
11. Del script a la herramienta.
12. La comprobación de la herramienta.
13. El resultado para la distribuidora: un plugin de QGIS.

:::{admonition} Tus datos
Para tu municipio, cambia en la entrada:

- el límite del municipio;
- la tabla de parques de bomberos: las coordenadas y los horarios, sácalos de las páginas públicas de tu servicio de emergencias;
- la capa de líneas, si tienes la tuya.

En España el Catastro es común a todas las comunidades salvo Navarra y el País Vasco, que tienen el suyo propio. Revisa las velocidades por tipo de carretera según tu relieve.
:::

## Resumen

### Conceptos nuevos

- **Pliego de condiciones:** el acuerdo sobre para quién construimos la herramienta, qué hay que darle de entrada, qué resultados devuelve, qué supuestos hacemos y cómo lo comprobaremos. Ejemplo: los seis resultados de nuestra herramienta.
- **Franja de seguridad:** la zona alrededor de la línea en la que, según la norma, no debe haber edificios. Ejemplo: nosotros la trazamos desde el eje de la línea, así que es una estimación por defecto.
- **Intervalo de distancia:** un escalón de distancia desde el borde de la franja. Ejemplo: «dentro de la franja».
- **Matriz de prioridad:** la tabla «distancia × tiempo → clase». Ejemplo: casa dentro de la franja y mucho tiempo de viaje, clase 1.
- **Límite inferior:** el valor por debajo del cual no puede estar la magnitud real. Ejemplo: nuestro tiempo de viaje.

### Lo más importante

- Primero el pliego, después el código.
- El resultado hace falta en tres niveles: edificios, tramos de línea y territorio.
- Todo lo que depende del lugar se define en la entrada, no en el código.
- La franja desde el eje y el tiempo con carreteras despejadas son estimaciones que pintan las cosas mejor de lo que son. Lo decimos claramente.
- Las reglas de comprobación se escriben antes del cálculo.

## Preguntas para un ingeniero

1. **¿Por qué no recortamos las carreteras justo por el límite del municipio?**

:::{dropdown} Respuesta
El parque de bomberos más cercano y la carretera más rápida pueden quedar al otro lado del límite. Una red recortada dará un tiempo equivocado, y el programa no mostrará ni un solo error.
:::

2. **La distribuidora pide una sola lista de casas ordenada por distancia a la línea. ¿Qué le faltará?**

:::{dropdown} Respuesta
Planifica su trabajo por tramos de línea, no por casas. Y necesita el tiempo de viaje: una casa junto a una línea cerca de un parque de bomberos es menos urgente que la misma casa a una hora de camino. Por eso el resultado son tramos con su clase según la matriz.
:::

3. **¿Por qué las reglas de comprobación se guardan en el repositorio antes del cálculo?**

:::{dropdown} Respuesta
La fecha en que se guardan demuestra que las reglas existían antes que el resultado. Así nos protegemos de la tentación de ajustar la comprobación a lo que ha salido.
:::

## Ejercicios

1. **Recordar.** ¿Qué tres números guardamos para cada edificio para poder comparar casas junto a líneas de distinta tensión? [Solución](#es-sol-1-1-1)

2. **Completar.** Escribe las palabras que faltan: «Las casas y las líneas las tomamos dentro del municipio, y las carreteras y los parques de bomberos, ______». [Solución](#es-sol-1-1-2)

3. **Recordar el prólogo.** ¿Qué pasaba en los dos partes de 2018 en los mismos municipios de montaña? [Solución](#es-sol-1-1-3)

## La pregunta abierta

El pliego está listo. Antes que nada, cualquier cálculo necesita un territorio: el límite del municipio. ¿Cómo lo obtenemos en QGIS con código y no con el ratón? ¿Y qué es, en realidad, Python dentro de QGIS? Con esto empieza la lección siguiente.

## Notas y fuentes

[^1]: [BOE, Real Decreto 223/2008, ITC-LAT 07, apartado 5.12.2](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf). Consultado el 19/11/2026.
[^2]: Ibídem: la distancia Dadd + Del = 3,3 + Del, con un mínimo de 5 m, se mide desde los conductores en su máxima desviación. Véase también [J. Andreu, «Distancias eléctricas de seguridad en LAAT», FEMPA, 2016](https://www.fempa.es/wp-content/uploads/oldfiles/documentos/20160203111250_Jornada%20distancias%20electricas%20020216_Jorge%20Andreu.pdf). Consultado el 19/11/2026.
[^3]: [BOE, Real Decreto 223/2008, ITC-LAT 01](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf): se considera alta tensión toda tensión nominal superior a 1 kV. Consultado el 19/11/2026.
[^4]: [Dirección General del Catastro, edificios INSPIRE](https://www.catastro.hacienda.gob.es/webinspire/index.html): atributos currentUse, numberOfDwellings y dateOfConstruction; véase también [IDEE, JIIDE 2017](https://www.idee.es/resources/presentaciones/JIIDE17/Apresentacoes_JIIDE2017_Conjunto_datos_edificios_BU_en_la_Direccion_General_del_Catastro_Pablo_Gomez_Dominguez.pdf). Consultado el 19/11/2026.
[^5]: [SITPA-IDEAS, Gobierno del Principado de Asturias: unidades administrativas](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/layers): capa de parroquias con datos de SADEI. Consultado el 19/11/2026.
[^6]: [Eurostat GISCO, malla de población GEOSTAT 2021](https://ec.europa.eu/eurostat/en/web/gisco/geodata/population-distribution/population-grids). Consultado el 19/11/2026.
[^7]: [112 Asturias, red de parques de bomberos del SEPA](https://www.112asturias.es/parques-bomberos). Consultado el 19/11/2026.
