## Esquema creado segun las especificaciones de los datos

```scala

import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._
val myDataSchem = StructType(
  Array(
    StructField("id", DecimalType(26, 0), true),
    StructField("anio", IntegerType, true),
    StructField("mes", IntegerType, true),
    StructField("provincia", IntegerType, true),
    StructField("canton", IntegerType, true),
    StructField("area", StringType, true),
    StructField("genero", StringType, true),
    StructField("edad", IntegerType, true),
    StructField("estado_civil", StringType, true),
    StructField("nivel_de_instruccion", StringType, true),
    StructField("etnia", StringType, true),
    StructField("ingreso_laboral", IntegerType, true),
    StructField("condicion_actividad", StringType, true),
    StructField("sectorizacion", StringType, true),
    StructField("grupo_ocupacion", StringType, true),
    StructField("rama_actividad", StringType, true),
    StructField("factor_expansion", DoubleType, true)
  )
);
```
### Carga de Datos(Data General)
```scala
import org.apache.spark.sql.functions.avg
val data = spark
    .read
    .schema(myDataSchem)
    .option("delimiter", "\t")
    .option("header","true")
    .csv("/home/shomira/Documentos/Datos_ENEMDU_PEA_v2.csv")
```
### Carga de Datos(Provincias)
```scala
val dataProv = spark
    .read
    .option("delimiter", ";")
    .option("header","true")
    .option("inferSchema", "true")
    .csv("/home/shomira/Documentos/cvsProvincias.csv")
```  
### Carga de Datos(Cantones)  
```scala
 val dataCant = spark
    .read
    .option("delimiter", ",")
    .option("header","true")
    .option("inferSchema", "true")
    .csv("/home/shomira/Documentos/Cantones.csv")
```  
## Construcción de nuestro DataFrame Final  
```scala
val innerProvince = data.join(dataProv, "provincia")
val dataProvCantones = innerProvince.join(dataCant, innerProvince("canton") === dataCant("codigoCanton"), "inner")
val dataProvCant = dataProvCantones.drop("canton", "codigoCanton")
```  
## FRECUENCIA DE DATOS EN LAS 4 ETNIAS PRINCIPALES  
Podemos ver que la frecuencia no es nada equitativa, y para realizar un mejor análisis solo se considerarán las cuatro etnias que más han participado en la encuesta.
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1595864391672_293456322?asIframe" style="width: 500px; height: 3000px; border: 0px"></iframe>  
### DataFrames de Interés
```scala
val dataInd = dataProvCant.where($"etnia" === "1 - Indígena")
val dataMon = dataProvCant.where($"etnia" === "5 - Montubio")
val dataMes = dataProvCant.where($"etnia" === "6 - Mestizo")
val dataBla = dataProvCant.where($"etnia" === "7 - Blanco")
val data4Etnias = dataProvCant.where($"etnia" === "1 - Indígena" || $"etnia" === "5 - Montubio" || $"etnia" === "6 - Mestizo" || $"etnia" === "7 - Blanco")  
```  
## Ingreso Laboral máximo de cada etnia (Global)  
La etnia Mestizo se encuentra con el ingreso laboral más elevado de 146030 llevando una ventaja abismal en comparación a las demás.;  
la etnia Blanco con 60000, que considerando que la población que participo en la encuesta de Indígena y Montubio es más elevada en comparación con etnia Blanco  aun así el ingreso laboral es más bajo.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1595864615527_-249742713?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>  
## Ingreso Laboral mínimo de cada etnia (Global)  
Aunque la tabla no muestra datos tan relevantes es muy importante tomar en cuenta que hay personas que en el campo Ingreso Laboral consta la cantidad cero.Mas adelante tomaremos en cuenta estos datos para realizar un análisis más detallado  
<iframe  src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173103083_1652105149?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>  
## Ingreso Laboral máximo de cada etnia (Por año)  
En la etnia MESTIZO es evidente que Su ingreso laboral máximo se mantiene con una gran ventaja respecto a las otras etnias en cada año, aún considerando que en el año 2019 su ingreso laboral maximo fue de 82200 casi la mitad del ingreso laboral que en el año 2017. El año 2019, de igual forma fue el más bajo para las etnias Blanco y Montubio; pero la etnia Indígena aumentó al menos 3 veces mas que en los años anteriores  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173125170_-2053718798?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>  
## Ingreso laboral mínimo de cada etnia (Por año)  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173171592_35795173?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>  
## Ingreso Laboral promedio de cada etnia (Global)  
El ingreso laboral promedio de cada etnia arrojó los resultados de que las etnia BLANCO y MESTIZO son las etnias con un ingreso laboral promedio más alto la primera con una cantidad de 634 y la segunda con 517, la etnia MONTUBIO y INDÍGENA tienen un promedio similar entre los 300 dólares.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173247055_309013628?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>  

## Ingreso Laboral promedio de cada etnia (Por año)  
El ingreso laboral promedio de cada etnia arrojó los resultados de que las etnia BLANCO y MESTIZO son las etnias con un ingreso laboral promedio más alto la primera con una cantidad de 634 y la segunda con 517, la etnia MONTUBIO y INDÍGENA tienen un promedio similar entre los 300 dólares  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173289405_-1925250001?asIframe" style="width: 600px; height: 130px; border: 0px"></iframe>  
## Porcentaje donde el campo Ingreso Laboral es Nulo, de acuerdo a cada Etnia  
En las gráficas de cada etnia muestran los resultados en porcentajes de la cantidad de personas que en el campo de ingreso laboral lo dejaron vacío, dado que es una consulta que retorna si es verdadero o falso, true significa que el campo de ingreso laboral es NULO (esta vacío), y false si el campo de ingreso laboral tiene datos.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173392198_404145140?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>  
## Porcentaje donde Ingreso Laboral sea menor al salario básico, de acuerdo a cada Etnia  
Para el análisis se ha considerado que el Ingreso laboral mínimo es $400, la gráfica muestra por cada etnia el total de personas en porcentajes que tienen un ingreso laboral menor al básico.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596349961379_1171444969?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>

## Sector en el que se ubican las personas que tienen un ingreso laboral de 0

<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596350435119_-2089224063?asIframe" style="width: 500px; height: 300px; border: 0px"></iframe>
## Distribución de los Grupos de Ocupación según cada Etnia  
En la gráfica podemos observar que para las etnias **Mestizo** y **Blanco** sus datos se ubican con mayor frecuencia en el grupo **Trabajad. de los servicios y comerciantes**; la etnia **Montubio** en el grupo **Trabajad. calificados agropecuarios y pesquero**; y la **Mestiza** en **Trabajadores no calificados, ocupaciones elementales**.  
Cabe recalcar además, que estos grupos son frecuentes en todas las etnias.  
<iframe src="https://48ba5146c5af.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173520999_705556833?asIframe" style="width: 700px; height: 400px; border: 0px"></iframe>  
## Distribución de la Sectorización según cada Etnia  
Dada la frecuencia de cada etnia que visualizamos al inicio se puede deducir que en las etnias **Mestizo** y **Blanco**, alrededor de la mitad se encuentran en el sector formal, pero en las etnias Indígenas y Montubio un porcentaje muy bajo de su población se encuentran en este sector.  
<iframe src="https://48ba5146c5af.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173552666_1801237412?asIframe" style="width: 700px; height: 400px; border: 0px"></iframe>  
## Distribución de los Niveles de Instrucción según cada Etnia  
En el nivel de instrucción PRIMARIA es donde se ubica la mayor parte de población de cada una de las 4 etnias analizadas. De estas etnias resalta sobretodo la etnia Montubio, en la cual casi la mitad de su poblacion total está ubicado en este nivel; siendo más del doble que aquellos ubicados en el segundo nivel mas frecuente (Secundaria)  
<iframe src="https://48ba5146c5af.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173583108_-1121211631?asIframe" style="width: 700px; height: 400px; border: 0px"></iframe>  
## ¿Que porcentaje de personas que tienen un nivel de instrucción "Superior Universitario" ganan menos que el salario básico?  
La fracción azul representa los datos nulos, es decir que no ingresaron un valor.  
La fracción naranja representa las personas que tienen un nivel de instrucción universitario y que su ingreso laboral es menor que el salario básico.  
La fracción verde representa el porcentaje de la población que cuenta con un nivel de instrucción universitario y que su ingreso laboral es mayor que el salario básico. En cada una de las etnias vemos que el porcentaje es mayor al 50% con esto podemos afirmar que las personas que cuentan con un nivel de instrucción universitario tienen una mejor posibilidad de tener un sueldo mayor al salario básico  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173622266_-1711352095?asIframe" style="width: 700px; height: 400px; border: 0px"></iframe>  
## Distribución de las Ramas de Actividad según cada Etnia  
Para cada una de las etnias, se puede visualizar que la rama  **A. Agricultura, ganadería caza y silvicultura y pesca** es la más frecuente,y con gran ventaja, en los datos; seguida, así mismo en todas las etnias, por la rama  **G. Comercio, reparación vehículos**  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173662604_-1103614208?asIframe" style="width: 600px; height: 400px; border: 0px"></iframe>  
## Distribución de personas en cada rama de actividad de aquellos ubicados en el SECTOR INFORMAL según cada etnia
En esta gráfica, basados en la distrubución de las ramas de actividad, nos damos cuenta que la mayor parte de los que se encontraban en la rama de actividad **A. Agricultura, ganadería caza y silvicultura y pesca** se ubican en el sector informal; siendo mas notorio en la etnia **Indídena**, pues casi su totalidad se ha encontrado en este sector  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173392198_404145140?asIframe" style="width: 500px; height: 300px; border: 0px"></iframe>  
## Distribución de personas en cada rama de actividad que tengan un nivel de instrucción primaria según cada etnia  
Aquí vemos que gran parte de la los que se encuetran en la rama **A. Agricultura, ganadería caza y silvicultura y pesca**, han correspondido a aquellos que se encuantan en el nivel de instruccion **Primaria**, esta ves constando aproximadamente la mitad de los que se encontraban en esta rama.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173991582_-354416295?asIframe" style="width: 700px; height: 400px; border: 0px"></iframe>  
## Distribución de personas en cada rama de actividad que tengan un nivel de instrucción secundaria según cada etnia
En esta gráfica, ya podemos notar un cambio en la etnia **Mestizo** pues la rama **G. Comercio, reparación vehículos** se posiciono como la de mayor freccuencia, mostrandonos que en esta etnia, un solo nivel mayor de instrucción permite el paso de productores, a trabajos más especializados.
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596174034680_-77341744?asIframe" style="width: 700px; height: 500px; border: 0px"></iframe>  
## Numero de mujeres por cada año y por etnia que pertenecieron a la rama_actividad  Agricultura, ganadería caza y silvicultura y pesca  
Podemos observar en la gráfica, que en esta rama para las etnias **Mestizo** y **Blanco** en cierta forma se ha mantenido la frecuencia de mujeres, sin variaciones muy significativas. En cambio, para la etnia **Montubio** ha tenido un incremento a través de los años, contrario a la **Indígena** que va en decremento.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596174069678_742123978?asIframe" style="width: 500px; height: 300px; border: 0px"></iframe>  
# OUTLIERS 
## OUTLIERS SUELDO  
## Ingresos Laborales mínimos de los outliers de cada etnia 
La gráfica muestra el ingreso laboral mínimo de la data con outliers, Se puede observar que el ingreso laboral mas bajo es de 1525 en la etnia Indígena y la etnia que tiene el ingreso laboral mínimo más alto es la etnia Blanco con 4900.
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596419818746_-1031120901?asIframe" style="width: 500px; height: 400px; border: 0px"></iframe>  
## Distribución según el grupo ocupación de cada etnia (Outliers Ingreso laboral)
A diferencia de la data general, aquí podemos observar que para la etnia **Mestizo** y **Blanco** prima el grupo de **Profesionales científicos e intelectuales**. En la etnia **Indígena** igualmente se ve un cambio, siendo ahora el principal grupo el de **Trabajad. de los servicios y comerciantes**. En la etnia **Montubio** pasa algo aun mas curioso, pues su grupo (**Trabajad. calificados agropecuarios y pesqueros**) se mantuvo igual con los datos que son Outliers  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596419921718_799536291?asIframe" style="width: 500px; height: 400px; border: 0px"></iframe>  
## Distribución según el nivel de instrucción de cada etnia (Outliers Ingreso laboral)
Aquí tambien podemos observar cambios respecto a la data general, pues para todas las etnias ahora prima el nivel **Superior Universitario**, incluso, para la etnia **Mestizo** es coonsiderable el nivel **Post-grado**, pero en las etnias **Indígena** y **Montubio** el nivel **Primaria** y **Secunadaria** tiene un peso casi igual que el  **Superior Universitario**, lo que indicaría que en estas etnias el exito economico no esta determinado por el nivel de instrucción como sucede en las otras etnias.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596420009619_1283470241?asIframe" style="width: 500px; height: 400px; border: 0px"></iframe>  
## Distribución según la provincia de cada etnia (Outliers Ingreso laboral)
Se procedió hacer el análisis de las personas que tienen los sueldos más elevados, ¿En qué provincia del país se encuentran?. Se encuentran en la provincia de PICHINCHA que es es la que resalta en relación con las otras provincias, seguido esta TUNGURAHUA, GUAYAS Y AZUAY .
La gráfica destaca la mayor densidad de personas con relación a indígenas y blancos, en las provincias de Guayas, Los Ríos, Manabí y Pichincha.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596420193049_1250457535?asIframe" style="width: 500px; height: 200px; border: 0px"></iframe>  
## OUTLIERS EDAD  
 ## Edad mínima de los outliers de cada etnia 
Por cada etnia se ha realizado el análisis de la edad mínima de las etnias, observando que la edad mínima menor es 87 en la etnia **Mestizo** y la edad mínima más alta es 96 en la etnia **Montubio**.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596420436261_-1148611801?asIframe" style="width: 500px; height: 200px; border: 0px"></iframe>  
## Ingresos Laborales promedio de los outliers de edad de cada etnia
 El menor promedio de ingreso lo tenemos en la etnia Indígena, que es de 45 $, y el ingreso promedio más alto está en la etnia Montubio con 381 $.  
 Se sabe que las personas consideradas aquí tienen mínimo 87 años, y  han llegado a ganar un sueldo mucho menor que el salario básico, llegando a no ser ni la cuarta parte del mismo.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596420468973_1324521546?asIframe" style="width: 500px; height: 300px; border: 0px"></iframe>  
 ## Distribución según el nivel de instrucción de cada etnia (Outliers Edad)
Analizando el nivel de instrucción de esta población, por cada etnia se evidencia que la **Mestizo** e **Indígena** radican en los niveles **Primaria** y **Ninguno**. Implicando esto la importancia de un buen nivel de instrucción.  
<iframe src="https://59da25dd4f56.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596420547727_-1112385428?asIframe" style="width: 600px; height: 300px; border: 0px"></iframe>



# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/Shomira/Present/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
