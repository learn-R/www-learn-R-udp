---
title: "Regresiones lineales, predictores categóricos y representación gráfica"
linktitle: "6: Regresiones lineales, predictores categóricos y representación gráfica"
date: "2021-08-13"
menu:
  example:
    parent: Ejemplos
    weight: 4
type: docs
toc: true
editor_options: 
  chunk_output_type: console
---

# 0. Objetivo de la práctica

El objetivo del práctico, es avanzar en el análisis de los datos a través del uso de **regresiones lineales**, para esto usaremos datos previamente procesados de la base de datos a utilizar. Recordemos que estamos en el proceso de análisis

![](https://github.com/learn-R/slides/raw/main/img/01/flow-rproject.png)

Entonces, en esta práctica aprenderemos trabajar las __regresiones lineales__, también trabajaremos los __predictores categóricos__ y finalmente veremos como __representarlos mediante gráficos y tablas__. 


# 1. Recursos del práctico

En este práctico utilizamos los **datos procesados** de la [**Encuesta Suplementaria de Ingresos (ESI) 2020**](https://www.ine.cl/estadisticas/sociales/ingresos-y-gastos/encuesta-suplementaria-de-ingresos).Recuerden _**siempre**_ consultar el [**libro códigos**](https://www.ine.cl/docs/default-source/encuesta-suplementaria-de-ingresos/bbdd/manual-y-gu%C3%ADa-de-variables/2020/personas-esi-2020.pdf?sfvrsn=f196cb4e_4) antes de trabajar datos.

````
- [<i class="fas fa-file-archive"></i> `06-bloque.zip`](https://github.com/learn-R/09-class/raw/main/06-bloque.zip)
````

# 2. Librerías a utilizar

Cargaremos los paquetes con `pacman` [revisar práctico 3](https://learn-r-udp.netlify.app/example/03-practico/) y utilizaremos `sjPlot` para la creación de tablas, `tidyverse` de este universo de paquetes utilizaremos `dplyr` (para la manipulación de datos) y `magrittr` (para utilizar el operador pipe) y finalmente la libreria `car` para la recodificación de datos. Recuerden que pueden ver más de las funciones de cada paquetes en la sección de *recursos*



```r
pacman::p_load(sjPlot, 
               tidyverse, 
               magrittr,
               car)
```


# 3. Importar datos

Una vez cargado los paquetes a utilizar, debemos cargar los datos procesados.




```r
load("output/data/datos_proc.RData")
```

## Explorar datos

Es relevante explorar los datos que utilizaremos, cómo están previamente procesados ¡no sabemos con que variables estamos trabajando!


```r
names(datos_proc)
```

```
## [1] "ingresos"  "educacion" "sexo"      "edad"
```



```r
head(datos_proc)
```

```
## # A tibble: 6 x 4
##   ingresos                                             educacion      sexo  edad
##      <dbl>                                             <dbl+lbl> <dbl+lbl> <dbl>
## 1  320421. 6 [Educación técnica (Educación superior no universi~ 2 [Mujer]    29
## 2  750000  7 [Educación universitaria]                           2 [Mujer]    30
## 3  900000  7 [Educación universitaria]                           2 [Mujer]    43
## 4       0  5 [Educación secundaria]                              2 [Mujer]    15
## 5       0  3 [Educación primaria (nivel 1)]                      1 [Hombr~    11
## 6       0  5 [Educación secundaria]                              2 [Mujer]    62
```


Ahora sabemos que trabajaremos con `"ingresos"`,  `"educacion"`, `"sexo"` y `"edad"`. Inclusive podemos repasar lo visto en el [práctico anterior](https://learn-r-udp.netlify.app/example/04-practico/)  y explorar nuestros datos con `sjPlot::view_df()`


```r
sjPlot::view_df(datos_proc,
                encoding = "UTF-8")
```

<table style="border-collapse:collapse; border:none;">
<caption>Data frame: datos_proc</caption>
<tr>
<th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">ID</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Name</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Label</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Values</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Value Labels</th>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ingresos</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Total ingresos sueldos y salarios</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-9312239.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">educacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ClasificaciÃ³n Internacional de Nivel Educacional<br>(CINE)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Nunca estudiÃ³<br>EducaciÃ³n preescolar<br>EducaciÃ³n primaria (nivel 1)<br>EducaciÃ³n primaria (nivel 2)<br>EducaciÃ³n secundaria<br>EducaciÃ³n tÃ©cnica (EducaciÃ³n superior no universitaria)<br>EducaciÃ³n universitaria<br>Postitulos y maestrÃ­a<br>Doctorado<br>Nivel ignorado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Hombre<br>Mujer</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">edad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Edad de la persona</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0-106</em></td>
</tr>

</table>

Pero previo a eso podemos visualizar que hay categorías que se pueden reducir en la variable `educacion`, por eso haremos un breve repaso del práctico anterior

### Recodificar

Como la variable `educacion` presenta la categoría de respuesta `Nivel ignorado` (casos perdidos) y casos que pueden unificarse como `Educación primaria (nivel 1)` y `Educación primaria (nivel 2)`, los asignaremos como NA y unificaremos. 

Para eso el primer paso es decirle a la base que transformaremos la variable como factor


```r
datos_proc$educacion <- as_factor(datos_proc$educacion)
```

Luego recodificaremos la variable con la función `recode` del paquete `car`


```r
datos_proc$educacion <- car::recode(datos_proc$educacion, recodes = c("'Nivel ignorado' = NA; 
                                                  c('Educación primaria (nivel 1)', 'Educación primaria (nivel 2)') = 'Educación primaria'"))
```

Finalmente visualizamos los cambios de nuestra base procesada con `view_df`     

<table style="border-collapse:collapse; border:none;">
<caption>Data frame: datos_proc</caption>
<tr>
<th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">ID</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Name</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Label</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Values</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Value Labels</th>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ingresos</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Total ingresos sueldos y salarios</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-9312239.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">educacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Doctorado<br>EducaciÃ³n preescolar<br>EducaciÃ³n primaria<br>EducaciÃ³n secundaria<br>EducaciÃ³n tÃ©cnica (EducaciÃ³n superior no universitaria)<br>EducaciÃ³n universitaria<br>Nunca estudiÃ³<br>Postitulos y maestrÃ­a</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Hombre<br>Mujer</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">edad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Edad de la persona</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0-106</em></td>
</tr>

</table>


Perfecto, podemos ver las variables que tenemos y sus categorías de respuesta, pero antes de continuar, es importante conocer los tipos de variables a usar, para eso pueden ir al mini tutorial de [**tipos de variables y ejemplos**](https://learn-r-udp.netlify.app/resource/r-datatypes-examples/)


# 4. Modelo de regresión

Previo al trabajo en R recordemos que la fórmula de la regresión lineal simple es:

`\begin{equation}
\widehat{Y}=b_{0} +b_{1}X
\end{equation}`

Mientras que en la regresión lineal múltiple es:

`\begin{equation}
\widehat{Y}=b_{0} +b_{1}X +b_{2}X +b_{x}X
\end{equation}`

Donde

- `\(\widehat{Y}\)` es el valor estimado/predicho de `\(Y\)`
- `\(b_{0}\)` es el **intercepto** de la recta (el valor de Y cuando las X's son 0)
- `\(b_{1}\)` y `\(b_{2}\)` son los **coeficientes de regresión**, que nos dice cuánto aumenta Y por cada punto que aumenta X (pendiente)


Les mostramos esto porque de la misma forma se diferencian ambos procedimientos en R

Para la regresión lineal simple se utiliza la siguiente estructura:

```
objeto <- lm(dependiente ~ independiente, data=datos)
```

Mientras que para la regresión lineal múltiple, sólo se añaden más variables


```
objeto <- lm(dependiente ~ independiente1 + independiente 2 + independientex, data=datos)
```


## Regresión lineal simple

Ahora en nuestros datos queda de la siguiente manera 


```r
reg_1 <-lm((ingresos ~ edad), data = datos_proc)
reg_1
```

```
## 
## Call:
## lm(formula = (ingresos ~ edad), data = datos_proc)
## 
## Coefficients:
## (Intercept)         edad  
##      102548         1530
```

pero el problema es que al observar el objeto creado, no es muy presentable para informes, por eso usaremos la función `tab_model` de `sjPlot`, que tiene la siguiente estructura:


```r
sjPlot::tab_model(objeto_creado, 
                  show.ci= F/T,  # este argumento muestra los intervalos de confianza
                  encoding = "UTF-8",  # evita errores en caracteres
                  file = "output/figures/reg1_tab.doc") # guarda lo creado automáticamente
```


Ahora en nuestros datos se vería así:


```r
sjPlot::tab_model(reg_1, show.ci=FALSE,  encoding = "UTF-8", file = "output/figures/reg1_tab.doc")
```

<table style="border-collapse:collapse; border:none;">
<tr>
<th style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm;  text-align:left; ">&nbsp;</th>
<th colspan="2" style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm; ">Total ingresos sueldos y<br>salarios</th>
</tr>
<tr>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  text-align:left; ">Predictors</td>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  ">Estimates</td>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  ">p</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">(Intercept)</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">102548.34</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "><strong>&lt;0.001</strong></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">Edad de la persona</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">1529.88</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "><strong>&lt;0.001</strong></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; padding-top:0.1cm; padding-bottom:0.1cm; border-top:1px solid;">Observations</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left; border-top:1px solid;" colspan="2">71935</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; padding-top:0.1cm; padding-bottom:0.1cm;">R<sup>2</sup> / R<sup>2</sup> adjusted</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left;" colspan="2">0.007 / 0.007</td>
</tr>

</table>



## Regresión múltiple

Ahora queremos incorporar las demás variables al modelo, para lo haremos de la siguiente manera


```r
reg_2 <-lm((ingresos ~ edad + sexo), data = datos_proc)
```



```r
sjPlot::tab_model(reg_2, show.ci=FALSE,  encoding = "UTF-8", file = "output/figures/regnc_tab.doc")
```

<table style="border-collapse:collapse; border:none;">
<tr>
<th style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm;  text-align:left; ">&nbsp;</th>
<th colspan="2" style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm; ">Total ingresos sueldos y<br>salarios</th>
</tr>
<tr>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  text-align:left; ">Predictors</td>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  ">Estimates</td>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  ">p</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">(Intercept)</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">237220.44</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "><strong>&lt;0.001</strong></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">Edad de la persona</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">1646.83</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "><strong>&lt;0.001</strong></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">Sexo</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;91039.16</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "><strong>&lt;0.001</strong></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; padding-top:0.1cm; padding-bottom:0.1cm; border-top:1px solid;">Observations</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left; border-top:1px solid;" colspan="2">71935</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; padding-top:0.1cm; padding-bottom:0.1cm;">R<sup>2</sup> / R<sup>2</sup> adjusted</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left;" colspan="2">0.020 / 0.020</td>
</tr>

</table>


¡Pero espera! ¡`sexo` no es una variable continua!

## Predictores categoricos 

Previo a esto hay que recordar que `sexo` no es un predictor continuo, y también debemos recordárselo a la base de datos (la variable `educación` tampoco lo es, pero ya la transformamos con `as_factor`)


```r
datos_proc$sexo <- as_factor(datos_proc$sexo)
```

Perfecto ahora si podemos añadir predictores categóricos a nuestra regresión múltiple


```r
reg_2 <-lm((ingresos ~ edad + sexo), data = datos_proc)
reg_3 <-lm((ingresos ~ edad + sexo + educacion), data = datos_proc)
```

Pero que pasa si queremos incluir todos los modelos creados en una sola tabla, para eso usaremos nuevamente la función `tab_model` de `sjPlot` 


```r
sjPlot::tab_model(list(reg_1, reg_2, reg_3), # los modelos estimados
  show.ci=FALSE, # no mostrar intervalo de confianza (por defecto lo hace)
  p.style = "stars", # asteriscos de significación estadística
  dv.labels = c("Modelo 1", "Modelo 2", "Modelo 3"), # etiquetas de modelos o variables dep.
  string.pred = "Predictores", string.est = "β", # nombre predictores y símbolo beta en tabla
  encoding =  "UTF-8",
  file = "output/figures/reg_tab_all.doc")
```

<table style="border-collapse:collapse; border:none;">
<tr>
<th style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm;  text-align:left; ">&nbsp;</th>
<th colspan="1" style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm; ">Modelo 1</th>
<th colspan="1" style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm; ">Modelo 2</th>
<th colspan="1" style="border-top: double; text-align:center; font-style:normal; font-weight:bold; padding:0.2cm; ">Modelo 3</th>
</tr>
<tr>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  text-align:left; ">Predictores</td>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  ">ÃŸ</td>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  ">ÃŸ</td>
<td style=" text-align:center; border-bottom:1px solid; font-style:italic; font-weight:normal;  ">ÃŸ</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">(Intercept)</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">102548.34 <sup>***</sup></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">146181.28 <sup>***</sup></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">1774294.45 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">Edad de la persona</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">1529.88 <sup>***</sup></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">1646.83 <sup>***</sup></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">1134.98 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">Sexo: Mujer</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;91039.16 <sup>***</sup></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;94042.51 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">educacion: EducaciÃ³n<br>preescolar</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;1734569.27 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">educacion: EducaciÃ³n<br>primaria</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;1732259.96 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">educacion: EducaciÃ³n<br>secundaria</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;1632080.40 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">educacion: EducaciÃ³n<br>tÃ©cnica(EducaciÃ³n<br>superior no<br>universitaria)</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;1497544.27 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">educacion: EducaciÃ³n<br>universitaria</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;1342780.60 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">educacion: Nunca estudiÃ³</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;1743566.17 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; ">educacion: Postitulos y<br>maestrÃ­a</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  "></td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:center;  ">&#45;641544.23 <sup>***</sup></td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; padding-top:0.1cm; padding-bottom:0.1cm; border-top:1px solid;">Observations</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left; border-top:1px solid;" colspan="1">71935</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left; border-top:1px solid;" colspan="1">71935</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left; border-top:1px solid;" colspan="1">71346</td>
</tr>
<tr>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; text-align:left; padding-top:0.1cm; padding-bottom:0.1cm;">R<sup>2</sup> / R<sup>2</sup> adjusted</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left;" colspan="1">0.007 / 0.007</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left;" colspan="1">0.020 / 0.020</td>
<td style=" padding:0.2cm; text-align:left; vertical-align:top; padding-top:0.1cm; padding-bottom:0.1cm; text-align:left;" colspan="1">0.202 / 0.201</td>
</tr>
<tr>
<td colspan="4" style="font-style:italic; border-top:double black; text-align:right;">* p&lt;0.05&nbsp;&nbsp;&nbsp;** p&lt;0.01&nbsp;&nbsp;&nbsp;*** p&lt;0.001</td>
</tr>

</table>

Ahora podemos observar que a diferencia de la tabla anterior la variable `sexo`, tiene incluida la categoría de respuesta de comparación.

# 5. Visualización 

Para visualizar o graficar los coeficientes de regresión para poder observar el impacto de cada variable en el modelo utilizaremos la función `plot_model` de `sjPlot`, su estructura es la siguiente:


```r
sjPlot::plot_model(objeto_creado, 
                   ci.lvl = "", #estima el nivel de confianza 
                   title = "",  # es el título
                   vline.color = "") # color de la recta vertical
```

Esto visualizado con nuestro modelo se ve así:


```r
sjPlot::plot_model(reg_3, ci.lvl = c(0.95), title = "Estimación de predictores", vline.color = "purple")
```

<img src="/example/06-practico_files/figure-html/unnamed-chunk-8-1.png" width="672" />

Terminamos por este práctico ¡Pero aún falta la regresión logística!

# 6. Resumen

En este práctico aprendimos a 

- Crear y visualizar regresiones lineales
- Incorporar predictores categóricos
- Crear y visualizar regresiones múltiples

# 7. Recursos

- [sjPlot](https://strengejacke.github.io/sjPlot/) 
- [tidyverse](https://www.tidyverse.org/packages/)
- [magrittr](https://magrittr.tidyverse.org/)

# 8. Reporte de progreso

¡Recuerda rellenar tu [reporte de progreso](https://learn-r.formr.org). En tu correo electrónico está disponible el código mediante al cuál debes acceder para actualizar tu estado de avance del curso.
