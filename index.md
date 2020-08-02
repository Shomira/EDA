## Esquema creado segun las especificaciones de los datos

You can use the [editor on GitHub](https://github.com/Shomira/Present/edit/master/index.md) to maintain and preview the content for your website in Markdown files.
```

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
```
import org.apache.spark.sql.functions.avg
val data = spark
    .read
    .schema(myDataSchem)
    .option("delimiter", "\t")
    .option("header","true")
    .csv("/home/shomira/Documentos/Datos_ENEMDU_PEA_v2.csv")
```
### Carga de Datos(Provincias)
```
val dataProv = spark
    .read
    .option("delimiter", ";")
    .option("header","true")
    .option("inferSchema", "true")
    .csv("/home/shomira/Documentos/cvsProvincias.csv")
```
### Unión en base a las columnas en común(código de Provincia)
- Spark no soporta columnas del mismo nombre y las elimina 
```
val innerProvince = data.join(dataProv, "provincia")
```

### Carga de Datos(Cantones)
```
 val dataCant = spark
    .read
    .option("delimiter", ",")
    .option("header","true")
    .option("inferSchema", "true")
    .csv("/home/shomira/Documentos/Cantones.csv")

```
### Unión en base a las columnas en común(código de Cantón )
- uso de la data que tienen los nombres de las provincias
```
val dataProvCantones = innerProvince.join(dataCant, innerProvince("canton") === dataCant("codigoCanton"), "inner")
```
### Eliminar las columnas que no se Usaran
```
val dataProvCant = dataProvCantones.drop("canton", "codigoCanton")
```
## FRECUENCIA DE DATOS EN LAS 4 ETNIAS PRINCIPALES
<iframe src="http://3533a2628400.ngrok.io /#/notebook/2FF95PR8V/paragraph/paragraph_1595864391672_293456322?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.
<iframe src="https://3533a2628400.ngrok.io/#/notebook/2FG2VTV4Y/paragraph/paragraph_1596142102972_454259431?asIframe" style= "width: 400px; height: 400px; border = 0px"></iframe>




### Porcentajes

<iframe src="https://3533a2628400.ngrok.io/#/notebook/2FF95PR8V/paragraph/paragraph_1596173392198_404145140?asIframe" style="width: 500px; height: 130px; border: 0px"></iframe>
Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
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
