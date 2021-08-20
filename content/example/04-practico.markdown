---
title: "Descriptivos"
linktitle: "4: Descriptivos"
date: "2021-08-23"
menu:
  example:
    parent: Ejemplos
    weight: 4
type: docs
toc: true
editor_options: 
  chunk_output_type: console
---

# 0. Objetivo del práctico

El objetivo del práctico, es avanzar en el análisis de los datos a través del uso de **estadísticos descriptivos**. Para esto ya debemos contar con datos previamente [procesados del práctico N°3](/example/03-practico). Recordemos en qué parte del proceso estamos

![](https://github.com/learn-R/slides/raw/main/img/01/flow-rproject.png)


En este práctico veremos tanto la _estimación puntual_ de estadísticos descriptivos, como su _visualización_ para reportes, ya sea a través de tablas o de gráficos. 


##  Recursos del práctico

En este práctico utilizamos los **datos** procesados *CASEN 2020*, que proviene de los datos originales de [**Encuesta de Caracterización Socioeconómica (CASEN)**](http://observatorio.ministeriodesarrollosocial.gob.cl/encuesta-casen-en-pandemia-2020).Recuerden _**siempre**_ consultar el [**libro códigos**](http://observatorio.ministeriodesarrollosocial.gob.cl/storage/docs/casen/2020/Libro_de_codigos_Base_de_Datos_Casen_en_Pandemia_2020.pdf) antes de trabajar datos.

- [<i class="fas fa-file-archive"></i> `04-bloque.zip`](https://github.com/learn-R/06-class/raw/main/04-bloque.zip)


# 1. Paquetes a utilizar

Para este práctico utilizaremos principalmente, las librerías `sjmisc` y `sjPlot`. 

`sjmisc`: esta paquete tiene múltiples funciones, desde la transformación de datos y variables. Este paquete suele complementar a  `dplyr` de `tidyverse` en sus funciones. 

`sjPlot`: su principal función es la visualización de datos para estadística en ciencias sociales mediante tablas y gráficos.

Cargaremos los paquetes con `pacman` [revisar práctico anterior](https://learn-r-udp.netlify.app/example/03-practico/)


```r
pacman::p_load(sjmisc,
               sjPlot,
               tidyverse,
               magrittr)
theme_set(theme_sjplot2())
```

# 2. Importar datos

Una vez cargado los paquetes a utilizar, debemos pasar al segundo paso: cargar los datos. Como indicamos al inicio, seguiremos utilizando los datos **CASEN* que fue procesada en el práctico anterior, pero le añadimos una variable.




```r
load("output/data/datos_proc.RData")
```

# 3. Explorar datos

Pero ¿cómo sabremos cuales son las variables que componen la base de datos procesada?, para ello usaremos dos códigos para conocer la **base procesada** que usaremos:

el código `names`, nos entrega los nombres de las variables que componen el data set


```r
names(datos_proc)
```

```
## [1] "sexo"              "edad_tramo"        "ocupacion"        
## [4] "horas_mens"        "ingreso_percapita" "ife"
```

Mientras que la función `head` nos entrega el nombre y las primeras 10 filas que la componen.


```r
head(datos_proc)
```

```
## # A tibble: 6 x 6
##         sexo edad_tramo ocupacion horas_mens ingreso_percapita       ife
##    <dbl+lbl> <chr>      <fct>          <dbl>             <dbl> <dbl+lbl>
## 1 2 [Mujer]  Joven      No                NA           195416.    1 [Sí]
## 2 1 [Hombre] Adulto     Sí               180           315861     2 [No]
## 3 2 [Mujer]  Joven      No                NA           315861     2 [No]
## 4 1 [Hombre] Adulto     Sí                45          1001389     2 [No]
## 5 1 [Hombre] Joven      No                NA          1001389     2 [No]
## 6 2 [Mujer]  Adulto     No                NA          1001389     2 [No]
```

Gracias a estos códigos sabemos que tenemos una aproximación de las variables que podríamos utilizar. Por el práctico anterior sabemos que podemos explorar nuestros datos con `sjPlot::view_df()`

<table style="border-collapse:collapse; border:none;">
<caption>Data frame: datos_proc</caption>
<tr>
<th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">ID</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Name</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Label</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Values</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Value Labels</th>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Hombre<br>Mujer</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">edad_tramo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">&lt;output omitted&gt;</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ocupacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">o1. La semana pasada, Â¿trabajÃ³ al menos una hora?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">SÃ­<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">horas_mens</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">y2_hrs. NÃºmero de horas mensuales pactadas con<br>empleador</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-720</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ingreso_percapita</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingreso total del hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-225200000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ife</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">y26d_hog. Ãšltimos 12 meses, Â¿alguien recibiÃ³<br>Ingreso Familiar de Emergencia?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">SÃ­<br>No<br>No sabe</td>
</tr>

</table>

# 4. Descripción de variables

Una vez conocidas las variables que incluye nuestros datos procesados, ¿cómo podemos realizar un análisis descriptivo para algún informe o reporte? Veamos algunas de las más comunes

## 4.1. Medidas de tendencia central

Para conocer las medidas de tendencia central de las variables hay dos opciones. En la **primera** se puede pedir el estadístico manualmente, en la **segunda** se puede pedir una tabla resumen. 

### Media

Para conocer la media de una variable se utiliza la función `mean()`, su estructura es:


```r
mean(datos$variable, na.rm=TRUE)
```


El argumento `na.rm=TRUE` excluye del cálculo a los casos perdidos. Esto aplicado a nuestra variable `ingreso_percapita` se ve así:


```r
mean(datos_proc$ingreso_percapita, na.rm=TRUE)
```

```
## [1] 355472.1
```

### Media recortada

Pero, ¿qué pasa si la variable `ingreso_percapita` esta influenciada por casos influyentes? Para eso puedo pedir la media recortada agregando el argumento `trim` para excluir al 5% de cada extremo


```r
mean(datos_proc$ingreso_percapita, na.rm=TRUE, trim = 0.025)
```

```
## [1] 305707.9
```

Aquí podemos ver que el valor es distinto. 

Ya conocimos el promedio de la variable `ingreso_percapita` podemos informar cuanto es el promedio del ingreso en el hogar en Chile, pero antes de eso queremos saber ¿cuánto gana el 50% de los hogares? Para eso calcularemos la **mediana**

### Mediana

Para el cálculo de la mediana se utiliza el comando median, su estructura es similar a la `mean()`:


```r
median(datos$variable, na.rm =TRUE)
```


```r
median(datos_proc$ingreso_percapita, na.rm =TRUE)
```

```
## [1] 229184.5
```

Ahora ya sabemos que al menos un 50% de las familias en Chile tienen por ingreso `$229.184`.

Ya tenemos los estadísticos principales, pero ¿cómo los reportamos? ¿tenemos que sacar el promedio de cada variable una por una? **¡No!**, para ello `sjmisc` tiene diferentes funciones, que veremos a continuación

## Un resumen

Podemos obtener un resumen de todos estadísticos a partir de la función `summary()`. El argumento puede ser tanto una columna en particular como `ingreso_percapita` 


```r
summary(datos_proc$ingreso_percapita)
```

```
##      Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
##         0    138612    229184    355472    390890 225200000
```

También podemos hacerlo con todos los datos. Sólo que no tendrá mucho sentido para las variables nominales.


```r
summary(datos_proc)
```

```
##       sexo        edad_tramo        ocupacion    horas_mens    
##  Min.   :1.000   Length:144418      Sí:60479   Min.   :  1.0   
##  1st Qu.:1.000   Class :character   No:83939   1st Qu.:146.0   
##  Median :2.000   Mode  :character              Median :180.0   
##  Mean   :1.547                                 Mean   :152.8   
##  3rd Qu.:2.000                                 3rd Qu.:180.0   
##  Max.   :2.000                                 Max.   :720.0   
##                                                NA's   :111080  
##  ingreso_percapita        ife       
##  Min.   :        0   Min.   :1.000  
##  1st Qu.:   138612   1st Qu.:1.000  
##  Median :   229184   Median :2.000  
##  Mean   :   355472   Mean   :1.766  
##  3rd Qu.:   390890   3rd Qu.:2.000  
##  Max.   :225200000   Max.   :9.000  
## 
```

`summary()` es una función muy potente, dado que no solo permite resúmenes de data.frames (datos), sino que también de otros objetos en R (como los modelos).

Ahora bien tiene limitantes para interactuar con `dplyr` y generar archivos de salida. Por ello ocuparemos `descr` de `sjmisc`


```r
sjmisc::descr(datos_proc$ingreso_percapita,
        show = "all",
        out = "viewer",
        encoding = "UTF-8",
        file = "output/figures/tabla-ingreso.doc")
```


```
## 
## ## Basic descriptive statistics
## 
##  var    type                   label      n NA.prc     mean       sd   se
##   dd numeric Ingreso total del hogar 144418      0 355472.2 834151.2 2195
##        md  trimmed                   range      iqr   skew
##  229184.5 266168.1 225200000 (0-225200000) 252277.6 152.42
```


De más variables


```r
datos_proc %>%
select(ingreso_percapita,  horas_mens) %>% 
sjmisc::descr(
  show = "all",
  out = "viewer",
  encoding = "UTF-8",
  file = "output/figures/tabla1.doc")
```


```
## 
## ## Basic descriptive statistics
## 
##                var    type
##  ingreso_percapita numeric
##         horas_mens numeric
##                                                     label      n NA.prc
##                                   Ingreso total del hogar 144418   0.00
##  y2_hrs. Número de horas mensuales pactadas con empleador  33338  76.92
##       mean        sd     se       md   trimmed                   range      iqr
##  355472.15 834151.23 2195.0 229184.5 266168.13 225200000 (0-225200000) 252277.6
##     152.83     54.69    0.3    180.0    159.83             719 (1-720)     34.0
##    skew
##  152.42
##   -0.98
```


Aquí podemos ver la variable `var`, el tipo de variable `type`, su etiqueta `label`, los casos válidos `n`, los casos perdidos `NA.prc`, las medidas de tendencia central y las de dispersión. `sjmisc` tiene muchos beneficios, ya que cómo interactúa con el mundo `tidyverse` es fácil de complementar con funciones como `select` de `dplyr`. Además con la función `file` se puede exportar automáticamente la tabla para los reportes.


## 4.3. Frecuencias 

### Frecuencias absolutas

Para conocer las frecuencias absolutas de una variable se podría usar la función `table`, esta nos arroja la frecuencia por cada categoría de respuesta


```r
table(datos_proc$sexo) 
```

```
## 
##     1     2 
## 65474 78944
```

También podríamos usar la función `flat_table`, esta puede agrupar más variables y agruparlas.


```r
flat_table(datos_proc, sexo, ocupacion, ife)
```

```
##                  ife    Sí    No No sabe
## sexo   ocupacion                        
## Hombre Sí             9695 23979     473
##        No            11263 19571     493
## Mujer  Sí             7527 18488     317
##        No            20084 31701     827
```

El problema es ¿cómo podríamos reportarla en nuestros informes? Si queremos una tabla general usaremos la función `frq`. Esta función devuelve una tabla de frecuencias de vectores etiquetados, como marco de datos.


```r
sjmisc::frq(datos_proc$sexo,
        out = "viewer",
         title = "Frecuencias",
        encoding = "UTF-8",
         file = "output/figures/tabla2.doc") 
```

<table style="border-collapse:collapse; border:none;">
<caption style="font-weight: bold; text-align:left;">Frecuencias</caption>
<tr>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; text-align:left;text-align:left; ">val</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; text-align: left;">label</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; text-align: right;">frq</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; text-align: right;">raw.prc</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; text-align: right;">valid.prc</th>
<th style="border-top: double; text-align:center; font-style:italic; font-weight:normal; padding:0.2cm; border-bottom:1px solid black; text-align: right;">cum.prc</th>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left;text-align:left; ">1</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: left;">Hombre</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">65474</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">45.34</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">45.34</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">45.34</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left;text-align:left; ">2</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: left;">Mujer</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">78944</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">54.66</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">54.66</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; text-align: right;">100.00</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left;text-align:left; border-bottom: 1px solid; ">NA</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: 1px solid; text-align: left;">NA</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: 1px solid; text-align: right;">0</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: 1px solid; text-align: right;">0.00</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: 1px solid; text-align: right;">NA</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center; border-bottom: 1px solid; text-align: right;">NA</td>
</tr>
<tr>
<td colspan="7" style="font-style:italic; border-top:double black; text-align:right;">total N=144418 &middot; valid N=144418 &middot; x&#772;=1.55 &middot; &sigma;=0.50
</td>
</tr>
</table>


# 5. Visualización

Ahora que ya sabemos como tener todos los estadísticos necesarios para escribir nuestros reportes, viene el segundo paso *visualizar los estadísticos*. Esto lo haremos con `sjPlot`

Para visualizar las frecuencias usaremos la función `plot_frq`, su estructura es la siguiente:


```r
plot_frq(datos,  #base
  ...,          #variable
  title = "",   # título
  type = c("bar", "dot", "histogram", "line", "density", "boxplot", "violin") #tipo de gráfico
```

Para los gráficos, tenemos los siguientes códigos

## 5.1. Gráfico de barras de frecuencias simple

Si quisiéramos presentar gráficos que entreguen la frecuencia de cada categoría de respuesta, podemos presentarla de la siguiente forma:



```r
plot_frq(datos_proc, edad_tramo,
          title = "Gráfico de frecuencias, barras",
          type = c("bar"))
```

<img src="/example/04-practico_files/figure-html/casen barras simple-1.png" width="672" />

{{< div "note" >}}

Además de la visualización es importante el guardar los datos que se producen y `sjPlot` tiene su propio código para hacerlo a través de la función `save_plot()`, su estructura es esta:

save_plot(last_plot())  #se deja el formato del archivo (.png, .jpg, .svg o .tif) y la ruta de la carpeta
{{< /div >}}

Así guardaríamos el gráfico anterior


```r
save_plot(last_plot("/output/img/tab.png"))
```


## 5.2. Gráfico de puntos

Si tenemos más categorías y queremos mejorar el reporte, podemos usar este código:


```r
plot_frq(datos_proc, edad_tramo,
          title = "Gráfico de frecuencias, puntos",
          type = c("dot"))
```

<img src="/example/04-practico_files/figure-html/casen puntos simple-1.png" width="672" />

También podemos cambiar el orden del eje x e y


```r
plot_frq(datos_proc$edad_tramo, type = "dot", show.ci = TRUE, sort.frq = "desc",
  coord.flip = TRUE, expand.grid = TRUE, vjust = "bottom", hjust = "left", title = "Gráfico de frecuencias, puntos cambiado"
)
```

<img src="/example/04-practico_files/figure-html/casen puntos cambiados-1.png" width="672" />
       
## 5.3 Histogramas        
       
Otra función que podemos hacer es graficar histogramas, sin embargo, como ya hemos visto, la variable `ingreso_percapita` tiene casos muy altos que distorsionan la variable. Para solucionar esto, ocuparemos lo aprendido en [el práctico anterior](https://learn-r-udp.netlify.app/example/03-practico/) y filtraremos la variable sacando los ingresos mayores a `$2.000.000`, con la función `filter` de `dplyr`


```r
datos_proc %>%  filter(ingreso_percapita <= 2000000) %>% 
plot_frq(., ingreso_percapita,
          title = "Histograma",
          type = c("histogram"))
```

<img src="/example/04-practico_files/figure-html/casen histograma-1.png" width="672" />

## 5.4 Densidad

Ahora que vemos la distribución del histograma, ¿cómo podemos ver su densidad?, es muy simple, para ello haremos un gráfico de densidad con el siguiente código


```r
datos_proc %>%  filter(ingreso_percapita <= 2000000) %>%
plot_frq(., ingreso_percapita,
          title = "Gráfico de densidad",
          type = c("density"))
```

<img src="/example/04-practico_files/figure-html/casen densidad-1.png" width="672" />

## 5.5 Gráfico de cajas

Para graficar los estadísticos de una variable, podemos hacerlo a través de un gráfico de cajas, para ello usaremos este código:


```r
datos_proc %>%  filter(ingreso_percapita <= 2000000) %>%
plot_frq(., ingreso_percapita,
          title = "Gráfico de caja",
          type = c("boxplot"))
```

<img src="/example/04-practico_files/figure-html/casen box-1.png" width="672" />

          
## 5.6 Gráfico de violín

Finalmente, si queremos presentar gráficos de violín, usamos este código
          

```r
datos_proc %>%  filter(ingreso_percapita <= 2000000) %>%
    plot_frq(., ingreso_percapita,
          title = "Gráfico de violín",
          type = c("violin"))
```

<img src="/example/04-practico_files/figure-html/casen violin-1.png" width="672" />
          
Como pueden ver, el único argumento que se modificaba era `type = `, es decir, para hacer diversos gráficos, sólo se debe especificar el tipo de gráfico que queremos.


## 5.7 Gráfico de nube de puntos

Ahora, si quisiéramos graficar la distribución de dos variables, podemos hacerlo con la función `plot_scatter`, esta muestra el diagrama de dispersión de dos variables.


```r
datos_proc %>%
  filter(ingreso_percapita <= 2000000, horas_mens <= 600) %>%
   plot_scatter(., horas_mens, ingreso_percapita)
```

<img src="/example/04-practico_files/figure-html/casen scatter-1.png" width="672" />

También es posible agregar una variable de ocupación al diagrama de dispersión.


```r
datos_proc %>%  filter(ingreso_percapita <= 2000000, horas_mens <= 600) %>%
    plot_scatter(., horas_mens, ingreso_percapita, edad_tramo)
```

<img src="/example/04-practico_files/figure-html/casen scatter 3 var-1.png" width="672" />

# 6. Visualización bivariada

Ahora que ya hemos graficado las frecuencias de las variables, vamos a graficar frecuencias agrupadas, para ello usaremos la función `plot:grpfrq` de `sjPlot`, su estructura es la siguiente


```r
plot_grpfrq(
  var.cnt,
  var.grp,
  type = c("bar", "dot", "line", "boxplot", "violin")
```

# 6.1 Gráfico de barras

La primera opción que nos entrega este código son los gráficos de barra, para usarlo queremos saber cuantos hombres y mujeres trabajaron al menos una hora la semana pasada, para ello graficaremos la variable `sexo` y `ocupacion`


```r
plot_grpfrq(datos_proc$sexo, datos_proc$ocupacion,
  type = c("bar"), title = "Gráfico de barras")
```

<img src="/example/04-practico_files/figure-html/casen frec agrup barr-1.png" width="672" />

Podemos ver que no solo nos muestra la frecuencia absoluta, sino que también la relativa en porcentaje

Pero además podemos ver agregar una tercera categoría, que es el total de ambas categorías. Para este ejercicio conoceremos que tramo de edad trabajo la semana pasada.

Para este ejercicio usaremos la función `plot_xtab`, de la misma librería


```r
plot_xtab(datos_proc$edad_tramo, datos_proc$ocupacion, title = "Gráfico de barras")
```

<img src="/example/04-practico_files/figure-html/casen frec agrup barr 2-1.png" width="672" />

# 6.2 Gráfico de barras horizontales

Con la misma función podemos graficar mediante barras horizontales


```r
plot_xtab(datos_proc$edad_tramo, datos_proc$ocupacion, margin = "row", 
          bar.pos = "stack",
          title = "Gráfico de barras horizontales",
         show.summary = TRUE, coord.flip = TRUE)
```

<img src="/example/04-practico_files/figure-html/casen frec agrup barr hor-1.png" width="672" />


## 6.3 Gráfico de líneas

Otra opción que tiene esta función, es la creación de gráficos de líneas, para ello conoceremos la relación entre el tramo etario y el recibir el IFE


```r
plot_grpfrq(datos_proc$edad_tramo, datos_proc$ife,
            title = "Gráfico de línea",
            type = c("line"))
```

<img src="/example/04-practico_files/figure-html/casen graf lin agrup-1.png" width="672" />

También podemos ver la relación de el tramo etario y si trabajó la semana pasada


```r
plot_grpfrq(datos_proc$edad_tramo, datos_proc$ocupacion, 
            title = "Gráfico de línea",
            type = "line")
```

<img src="/example/04-practico_files/figure-html/casen graf line agrup 2-1.png" width="672" />
         

## 6.4 Gráfico de cajas

Ahora si queremos conocer cómo interactúa las horas de trabajo con el tramo etario, podemos visualizarlo mediante un gráfico de cajas


```r
plot_grpfrq(datos_proc$horas_mens, datos_proc$edad_tramo,
            title = "Gráfico de caja",
             type = c("boxplot"))
```

<img src="/example/04-practico_files/figure-html/casen graf caj-1.png" width="672" />

Además, se puede incorporar una tercera variable, en este caso lo haremos con la variable `sexo`


```r
plot_grpfrq(datos_proc$horas_mens, datos_proc$edad_tramo, intr.var = datos_proc$sexo, 
            title = "Gráfico de cajas",
            type = "box")
```

<img src="/example/04-practico_files/figure-html/casen box 3 var-1.png" width="672" />

Nuevamente, la función nos permite la creación de múltiples gráficos, sólo se debe cambiar el argumento `type =`

# 7. Tablas de contingencia

¡No podemos terminar sin saber cómo hacer tablas de frecuencias cruzadas!

Por suerte `sjPlot` tiene la función `sjt.xtab`, que nos entrega tablas de frecuencias cruzadas


```r
sjt.xtab(datos_proc$sexo, datos_proc$ife,  title = "Tabla de contingencias",
         show.col.prc=TRUE,
         show.summary=FALSE)
```

<table style="border-collapse:collapse; border:none;">
<caption style="font-weight: bold; text-align:left;">Tabla de contingencias</caption>
 <tr>
 <th style="border-top:double; text-align:center; font-style:italic; font-weight:normal; border-bottom:1px solid;" rowspan="2">Sexo</th>
 <th style="border-top:double; text-align:center; font-style:italic; font-weight:normal;" colspan="3">y26d_hog. Ãšltimos 12<br>meses, Â¿alguien<br>recibiÃ³ Ingreso<br>Familiar de<br>Emergencia?</th>
 <th style="border-top:double; text-align:center; font-style:italic; font-weight:normal; font-weight:bolder; font-style:italic; border-bottom:1px solid; " rowspan="2">Total</th>
 </tr>
 
<tr>
 <td style="border-bottom:1px solid; text-align:center; padding:0.2cm;">SÃ­</td>
 <td style="border-bottom:1px solid; text-align:center; padding:0.2cm;">No</td>
 <td style="border-bottom:1px solid; text-align:center; padding:0.2cm;">No sabe</td>
 </tr>
 
<tr> 
<td style="padding:0.2cm;  text-align:left; vertical-align:middle;">Hombre</td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">20958</span><br><span style="color:#339933;">43.2&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">43550</span><br><span style="color:#339933;">46.5&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">966</span><br><span style="color:#339933;">45.8&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;  "><span style="color:black;">65474</span><br><span style="color:#339933;">45.3&nbsp;&#37;</span></td> 
</tr>
 
<tr> 
<td style="padding:0.2cm;  text-align:left; vertical-align:middle;">Mujer</td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">27611</span><br><span style="color:#339933;">56.8&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">50189</span><br><span style="color:#339933;">53.5&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">1144</span><br><span style="color:#339933;">54.2&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;  "><span style="color:black;">78944</span><br><span style="color:#339933;">54.7&nbsp;&#37;</span></td> 
</tr>
 
<tr> 
<td style="padding:0.2cm;  border-bottom:double; font-weight:bolder; font-style:italic; text-align:left; vertical-align:middle;">Total</td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">48569</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">93739</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">2110</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">144418</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td> 
</tr>
 
</table>

¿Qué pasó? ¿por qué salen esos símbolos raros en la tabla? 

¡Es por la codificación!, para ello le agregamos el argumento `encoding = "UTF-8"` y ya tenemos nuestra tabla de frecuencias cruzadas


```r
sjt.xtab(datos_proc$sexo, datos_proc$ife,
         show.col.prc=TRUE,
         show.summary=FALSE, 
         encoding = "UTF-8", 
         title = "Tabla de contingencia",
         file = "output/figures/tabla3.doc")
```

<table style="border-collapse:collapse; border:none;">
<caption style="font-weight: bold; text-align:left;">Tabla de contingencia</caption>
 <tr>
 <th style="border-top:double; text-align:center; font-style:italic; font-weight:normal; border-bottom:1px solid;" rowspan="2">Sexo</th>
 <th style="border-top:double; text-align:center; font-style:italic; font-weight:normal;" colspan="3">y26d_hog. Ãšltimos 12<br>meses, Â¿alguien<br>recibiÃ³ Ingreso<br>Familiar de<br>Emergencia?</th>
 <th style="border-top:double; text-align:center; font-style:italic; font-weight:normal; font-weight:bolder; font-style:italic; border-bottom:1px solid; " rowspan="2">Total</th>
 </tr>
 
<tr>
 <td style="border-bottom:1px solid; text-align:center; padding:0.2cm;">SÃ­</td>
 <td style="border-bottom:1px solid; text-align:center; padding:0.2cm;">No</td>
 <td style="border-bottom:1px solid; text-align:center; padding:0.2cm;">No sabe</td>
 </tr>
 
<tr> 
<td style="padding:0.2cm;  text-align:left; vertical-align:middle;">Hombre</td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">20958</span><br><span style="color:#339933;">43.2&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">43550</span><br><span style="color:#339933;">46.5&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">966</span><br><span style="color:#339933;">45.8&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;  "><span style="color:black;">65474</span><br><span style="color:#339933;">45.3&nbsp;&#37;</span></td> 
</tr>
 
<tr> 
<td style="padding:0.2cm;  text-align:left; vertical-align:middle;">Mujer</td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">27611</span><br><span style="color:#339933;">56.8&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">50189</span><br><span style="color:#339933;">53.5&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center; "><span style="color:black;">1144</span><br><span style="color:#339933;">54.2&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;  "><span style="color:black;">78944</span><br><span style="color:#339933;">54.7&nbsp;&#37;</span></td> 
</tr>
 
<tr> 
<td style="padding:0.2cm;  border-bottom:double; font-weight:bolder; font-style:italic; text-align:left; vertical-align:middle;">Total</td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">48569</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">93739</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">2110</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td>
<td style="padding:0.2cm; text-align:center;   border-bottom:double;"><span style="color:black;">144418</span><br><span style="color:#339933;">100&nbsp;&#37;</span></td> 
</tr>
 
</table>

# 8. Correlación

Ahora veremos estadísticos bivariados, como la correlación, en esta ocasión generaremos una tabla de correlación entre las variables `horas_mens` y `ingreso_percapita`, para eso usaremos la función `tab_corr` de `sjPlot`

Previamente debemos seleccionar las variables a utilizar, ya que no tiene sentido incluir en el análisis variables nominales


```r
datos_proc %>%
select(ingreso_percapita, horas_mens) %>% 
tab_corr(.,
         triangle = "lower",   
         title = "Tabla de correlación",
         encoding = "UTF-8", 
         file = "output/figures/tabla4.doc")
```

<table style="border-collapse:collapse; border:none;">
<caption style="font-weight: bold; text-align:left;">Tabla de correlaciÃ³n</caption>
<tr>
<th style="font-style:italic; font-weight:normal; border-top:double black; border-bottom:1px solid black; padding:0.2cm;">&nbsp;</th>
<th style="font-style:italic; font-weight:normal; border-top:double black; border-bottom:1px solid black; padding:0.2cm;">Ingreso total del hogar</th>
<th style="font-style:italic; font-weight:normal; border-top:double black; border-bottom:1px solid black; padding:0.2cm;">y2_hrs. NÃºmero de horas mensuales<br>pactadas con empleador</th>
</tr>
<tr>
<td style="font-style:italic;">Ingreso total del hogar</td>
<td style="padding:0.2cm; text-align:center;">&nbsp;</td>
<td style="padding:0.2cm; text-align:center;">&nbsp;</td>
</tr>
<tr>
<td style="font-style:italic;">y2_hrs. NÃºmero de horas mensuales<br>pactadas con empleador</td>
<td style="padding:0.2cm; text-align:center;">0.054<span style="vertical-align:super;font-size:0.8em;">***</span></td>
<td style="padding:0.2cm; text-align:center;">&nbsp;</td>
</tr>
<tr>
<td colspan="3" style="border-bottom:double black; border-top:1px solid black; font-style:italic; font-size:0.9em; text-align:right;">Computed correlation used pearson-method with listwise-deletion.</td>
</tr>
 
</table>


# 9. Anova

Finalmente, si queremos reportar un análisis de Anova, no podemos dejar de lado este gráfico que nos otorga la función `sjp.aov1` del paquete `sjPlot`


```r
sjp.aov1(datos_proc$ingreso_percapita, datos_proc$sexo, title = "Anova")
```

<img src="/example/04-practico_files/figure-html/unnamed-chunk-8-1.png" width="672" />

# 9. Resumen del práctico

¡Eso es todo por este práctico! Hoy aprendimos a:

- Manejar datos descriptivos en Rstudio
- A obtener tablas descriptivas
- A visualizar los descriptivos
- A obtener tablas de contingencia
- A obtener tablas de correlación
- A obtener gráficos de Anova

# 7. Reporte de progreso

¡Recuerda rellenar tu [reporte de progreso](https://learn-r.formr.org/). En tu correo electrónico está disponible el código mediante al cuál debes acceder para actualizar tu estado de avance del curso.

