## Esquema creado segun las especificaciones de los datos

You can use the [editor on GitHub](https://github.com/Shomira/Present/edit/master/index.md) to maintain and preview the content for your website in Markdown files.
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
### Unión en base a las columnas en común(código de Provincia)
- Spark no soporta columnas del mismo nombre y las elimina 
```scala
val innerProvince = data.join(dataProv, "provincia")
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
### Unión en base a las columnas en común(código de Cantón )
- uso de la data que tienen los nombres de las provincias
```scala
val dataProvCantones = innerProvince.join(dataCant, innerProvince("canton") === dataCant("codigoCanton"), "inner")
```
### Eliminar las columnas que no se Usaran
```scala
val dataProvCant = dataProvCantones.drop("canton", "codigoCanton")
```
## FRECUENCIA DE DATOS EN LAS 4 ETNIAS PRINCIPALES  
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1595864391672_293456322?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>  
  
  ### Datos de acuerdo  a cada Etnia  
  ```scala
val dataInd = dataProvCant.where($"etnia" === "1 - Indígena")
val dataMon = dataProvCant.where($"etnia" === "5 - Montubio")
val dataMes = dataProvCant.where($"etnia" === "6 - Mestizo")
val dataBla = dataProvCant.where($"etnia" === "7 - Blanco")
val data4Etnias = dataProvCant.where($"etnia" === "1 - Indígena" || $"etnia" === "5 - Montubio" || $"etnia" === "6 - Mestizo" || $"etnia" === "7 - Blanco")  
```  
## Ingreso Laboral máximo de cada etnia (Global)  
La etnia Mestizo se encuentra con el ingreso laboral más elevado de 146030 llevando una ventaja abismal en comparación a las demás;
la etnia Blanco con 60000, que considerando que la población que participo en la encuesta de Indígena y Montubio es más elevada en comparación con etnia Blanco aun así el ingreso laboral es más bajo.  
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1595864615527_-249742713?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>  
## Ingreso Laboral mínimo de cada etnia (Global)  
Aunque la tabla no muestra datos tan relevantes es muy importante tomar en cuenta que hay personas que en el campo Ingreso Laboral consta la cantidad cero.
Mas adelante tomaremos en cuenta estos datos para realizar un análisis más detallado  
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173103083_1652105149?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>  
## Ingreso Laboral máximo de cada etnia (Por año)
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173125170_-2053718798?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>  
## Ingreso laboral mínimo de cada etnia (Por año)  
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173171592_35795173?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>  
## Ingreso Laboral promedio de cada etnia (Global)  
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173247055_309013628?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>  
## Ingreso Laboral promedio de cada etnia (Por año)  
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173289405_-1925250001?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>

## Porcentaje donde el campo Ingreso Laboral es Nulo, de acuerdo a cada Etnia
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173392198_404145140?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
## Porcentaje donde Ingreso Laboral sea menor al salario básico, de acuerdo a cada Etnia

<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596349961379_1171444969?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>

## Sector en el que se ubican las personas que tienen un ingreso laboral de 0

<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596350435119_-2089224063?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
## Distribución de los Grupos de Ocupación según cada Etnia
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173520999_705556833?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
## Distribución de la Sectorización según cada Etnia
<iframe src="https://f883705c1fb4.ngrok.io#/notebook/2FF95PR8V/paragraph/paragraph_1596173552666_1801237412?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
## Distribución de los Niveles de Instrucción según cada Etnia
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173583108_-1121211631?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
## ¿Que porcentaje de personas que tienen un nivel de instrucción "Superior Universitario" ganan menos que el salario básico?

<iframe src="https://f883705c1fb4.ngrok.io#/notebook/2FF95PR8V/paragraph/paragraph_1596173622266_-1711352095?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
## 
<iframe src="https://f883705c1fb4.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173662604_-1103614208?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>

<iframe src="https://f883705c1fb4.ngrok.io#/notebook/2FF95PR8V/paragraph/paragraph_1596173392198_404145140?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

Syntax highlighted code block

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
