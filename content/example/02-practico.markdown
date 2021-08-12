---
title: "Transformar y seleccionar variables"
linktitle: "2: Transformar y seleccionar variables"
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

## 0. Objetivo de la práctica

El objetivo del práctico, es avanzar en el procesamiento de los datos a través de la transformación de las variables a utilizar. Para ello revisaremos procedimientos básicos para el manejo de datos con Rstudio.


## 1. Recursos de la práctica

En este práctico utilizaremos la base de datos de la [**Encuesta de Caracterización Socioeconómica (CASEN)**](http://observatorio.ministeriodesarrollosocial.gob.cl/encuesta-casen-en-pandemia-2020), la cual fue procesada en el [Práctico anterior]().Recuerden siempre consultar el [**manual/libro de códigos**](http://observatorio.ministeriodesarrollosocial.gob.cl/storage/docs/casen/2020/Libro_de_codigos_Base_de_Datos_Casen_en_Pandemia_2020.pdf) antes de trabajar una base de datos.


## 2. Librerias a utilizar

En este práctico utilizaremos cuatro paquetes

1. `pacman`: este facilita y agiliza la lectura de los paquetes a utilizar en R

2. `sjmisc`: explorar datos

3. `car()`: recodificar manteniendo las etiquetas

4. `tidyverse`: colección de paquetes, del cuál utilizaremos `dplyr`  `forcast`

5. `magrittr`: nos permitirá ocupar dos operadores que en R son **muy** utilizados


# Pasos del procesamiento

## 1. Cargar librerías

Primero instalamos `pacman` por única vez


```r
install.packages("pacman")
library (pacman)
```

Luego cargaremos así los paquetes. Les recordamos que cuando luego de un paquete ponemos `::` esto se refiere a que se *"fuerza"* que esa función provenga de *ese paquete*


```r
pacman::p_load(tidyverse,
               magrittr,
               car,
               sjmisc)
```


## 2. Cargar base de datos




```r
datos <- read_dta("../Rproject/input/Casen en Pandemia 2020 STATA.dta") 
```

En el panel **Environment**, visualizamos el nuevo objeto, que posee 185.437 observaciones (o filas), y 650 variables (columnas)

Recordemos que también podemos explorar la base de datos con las siguientes funciones.


```r
# De paquete base
dim(datos) # nos entrega las dimensiones, es decir el numero de observaciones y el número de variables
View(datos) # se usa para visualizar la base de datos
names(datos) # entrega los nombres de las variables que componen el dataset
head(datos) # muestra las primeras filas presentes en el marco de datos

# De sjmisc

find_var(datos, "concepto") # se utiliza para encontrar variables
```

## 4. Un aspecto clave antes de comenzar: los operadores


Previo a trabajar con la base de datos, debemos repasemos el concepto de **operadores**. Estos símbolos no son de uso exclusivo en R ¡probablemente los conoces desde tus cursos de matemática! Ahora bien, no todos tienen el mismo significado que en otros softwares.

Los **operadores** serán símbolos que permitirán, en los distintos procedimientos de procesamiento, simplificar procesos. Por ejemplo, serán útilizados cuando filtremos nuestros datos para personas de ciertas categorías, cuando calculemos variables nuevas (de manera aritmética o condicional) o, simplemente, cuando queramos hacer procesos "concatenados".


#### Operadores relacionales 

Se usan para hacer comparaciones. Cuando en la *Tabla 1* nos referimos a `un valor`, esto refiere tambien a `variables`

| Símbolo  | Función |
|---------:|:--------|
| `<`      |  Un valor es menor que otro |
| `>`      |  Un valor es mayor que otro |
| `==`     |  Un valor es igual que otro [^1] |
| `<=`     |  Un valor es menor o igual que otro |
| `>=`     |  Un valor es mayor o igual que otro |
| `!=`     |  Un valor es distinto o diferente que otro|
| `%in%`   |  Un valor pertenece al conjunto designado [^2] |
| `is.na()`|  El valor es perdido o `NA` |
| `!is.na()`| El valor es distinto de  `NA` |

[^1]: ¡Atención! Fíjate bien que `==` y `=` son distintos. En R `==` es indicar *"igual a"*, mientras que  `=` es *asignar* (sinónimo de `<-`)

[^2]: Este operador es **muy utilizado**, sirve para indicar que algo está dentro de una cadena de valores. 

#### Operadores aritméticos

Realizan operaciones, como la suma, resta, división, entre otros.

| Símbolo  | Función |
|---------:|:--------|
| `+`      |  Suma |
| `-`      |  Resta|
| `*`     |  Multiplicación |
| `/`     |  División |
| `^`     |  Elevado |

#### Operadores de asignación

Hay dos formas de asignar `objetoA <- objetoB` o `objetoA = objetoB`. Ambas implican que lo que se este realizando en el *objetoB* implica que eso va a producir o generar al *objetoA*

#### Operadores booleanos

Describen relaciones **lógicas** o **condicionales**

| Símbolo  | Función |
|---------:|:--------|
| `&`      |  Indica un *y* lógico |
| `|`      |  Indica un *o* lógico |
| `xor()`  |  Excluye la condición  |
| `!`      |  Distinto de ... |
| `any`    |  Ninguna de las condiciones serán utilizadas |
| `all`    |  Todas las condiciones serán ocupadas |


<img src="/img/example/02-practico/01operad.png" width="60%" />
Figura 1: Resumen de operadores

### Operador pipeline %>% 

¡Aquí mucha atención! Este operador `%>%` (llamado `pipe`) no es un operador que este contenido en las funciones base del lenguaje R. Este operador proviene de la función `magrittr` de `tidyverse`, y es de los operadores más útiles y utilizados en R.

¿Para qué sirve? Para concatenar múltiples funciones y procesos. *Imagina que quieres filtrar una base de datos a partir de tramos etarios. Pero no tienes esa variable creada. ¿Que hacer? La respuesta: concatenar el proceso de creación de variables y luego filtrar.* Eso se puede hacer gracias a ` %>% ` (ya mostraremos como utilizar esta herramienta), que por lo demás es muy fácil de ejecutar.

- `Ctrl + shift + M` Para Windows
- `⌘ + shift + M` Para Mac


## 5. Transformación y selección de variables con `dplyr()`

`dplyr()`es un paquete de `tidyverse` que proporciona una base para la manipulación de datos, principalmente a partir de funciones que permiten operar las columnas (o variables)

### 5.1 `select()` para manipular variables

Para **seleccionar variables** ocuparemos `select()`. Existen diversos enfoques para realizarlo. Primero, debemos conocer su estructura

Si queremos incluir las variables `variable1`, `variable2` y `variable3`
{{< div "note" >}}
select(datos, variable1, variable2, variable3)
{{< /div >}}

Si queremos excluir anteponemos un menos `-variable1`
{{< div "note" >}}
select(datos, -variable1)
{{< /div >}}


#### `select()` por indexación

La indexación refiere al orden que tiene la columna o variable dentro de los datos. Imaginemos que queremos seleccionar:


```r
select(datos, 1, 2) # la primera y la segunda columna
```

```
## # A tibble: 185,437 x 2
##           folio     o
##           <dbl> <dbl>
##  1 110110010101     1
##  2 110110010101     2
##  3 110110010201     2
##  4 110110010201     1
##  5 110110010201     3
##  6 110110010301     2
##  7 110110010301     3
##  8 110110010301     1
##  9 110110010401     1
## 10 110110010401     2
## # ... with 185,427 more rows
```

```r
select(datos, 1:4) # la primera hasta la cuarta columna
```

```
## # A tibble: 185,437 x 4
##           folio     o id_persona id_vivienda
##           <dbl> <dbl>      <dbl>       <dbl>
##  1 110110010101     1          5  1101100101
##  2 110110010101     2          6  1101100101
##  3 110110010201     2         31  1101100102
##  4 110110010201     1         32  1101100102
##  5 110110010201     3         30  1101100102
##  6 110110010301     2        117  1101100103
##  7 110110010301     3        118  1101100103
##  8 110110010301     1        116  1101100103
##  9 110110010401     1       2209  1101100104
## 10 110110010401     2       2210  1101100104
## # ... with 185,427 more rows
```

```r
select(datos, c(1,4)) # la primera y la cuarta columna
```

```
## # A tibble: 185,437 x 2
##           folio id_vivienda
##           <dbl>       <dbl>
##  1 110110010101  1101100101
##  2 110110010101  1101100101
##  3 110110010201  1101100102
##  4 110110010201  1101100102
##  5 110110010201  1101100102
##  6 110110010301  1101100103
##  7 110110010301  1101100103
##  8 110110010301  1101100103
##  9 110110010401  1101100104
## 10 110110010401  1101100104
## # ... with 185,427 more rows
```

#### `select()` por nombre de columna

Si conocemos el nombre de la variable simplemente lo podemos poner y se seleccionará.


```r
select(datos, edad, sexo, o1)
```

```
## # A tibble: 185,437 x 3
##     edad       sexo        o1
##    <dbl>  <dbl+lbl> <dbl+lbl>
##  1    34 2 [Mujer]     2 [No]
##  2     4 2 [Mujer]    NA     
##  3     5 2 [Mujer]    NA     
##  4    45 1 [Hombre]    1 [Sí]
##  5    19 2 [Mujer]     2 [No]
##  6    57 1 [Hombre]    1 [Sí]
##  7    20 1 [Hombre]    2 [No]
##  8    56 2 [Mujer]     2 [No]
##  9    77 1 [Hombre]    2 [No]
## 10    60 2 [Mujer]     2 [No]
## # ... with 185,427 more rows
```

También puedo renombrar en el mismo proceso de selección indicando `nuevo_nombre = nombre_original` en el proceso de selección


```r
select(datos, edad, sexo, ocupacion = o1)
```

```
## # A tibble: 185,437 x 3
##     edad       sexo ocupacion
##    <dbl>  <dbl+lbl> <dbl+lbl>
##  1    34 2 [Mujer]     2 [No]
##  2     4 2 [Mujer]    NA     
##  3     5 2 [Mujer]    NA     
##  4    45 1 [Hombre]    1 [Sí]
##  5    19 2 [Mujer]     2 [No]
##  6    57 1 [Hombre]    1 [Sí]
##  7    20 1 [Hombre]    2 [No]
##  8    56 2 [Mujer]     2 [No]
##  9    77 1 [Hombre]    2 [No]
## 10    60 2 [Mujer]     2 [No]
## # ... with 185,427 more rows
```

#### `select()` para reordenar variables

Podemos seleccionar variables y luego indicar que queremos todo el resto de las variables (`everythin()`). Solo por una cosa de orden. Esto será útil sobre todo cuando tengamos identificadores


```r
select(datos, id_persona, sexo, edad, everything())
```

```
## # A tibble: 185,437 x 650
##    id_persona      sexo  edad    folio     o id_vivienda       region  provincia
##         <dbl> <dbl+lbl> <dbl>    <dbl> <dbl>       <dbl>    <dbl+lbl>  <dbl+lbl>
##  1          5 2 [Mujer]    34  1.10e11     1  1101100101 1 [Región d~ 11 [Iquiq~
##  2          6 2 [Mujer]     4  1.10e11     2  1101100101 1 [Región d~ 11 [Iquiq~
##  3         31 2 [Mujer]     5  1.10e11     2  1101100102 1 [Región d~ 11 [Iquiq~
##  4         32 1 [Hombr~    45  1.10e11     1  1101100102 1 [Región d~ 11 [Iquiq~
##  5         30 2 [Mujer]    19  1.10e11     3  1101100102 1 [Región d~ 11 [Iquiq~
##  6        117 1 [Hombr~    57  1.10e11     2  1101100103 1 [Región d~ 11 [Iquiq~
##  7        118 1 [Hombr~    20  1.10e11     3  1101100103 1 [Región d~ 11 [Iquiq~
##  8        116 2 [Mujer]    56  1.10e11     1  1101100103 1 [Región d~ 11 [Iquiq~
##  9       2209 1 [Hombr~    77  1.10e11     1  1101100104 1 [Región d~ 11 [Iquiq~
## 10       2210 2 [Mujer]    60  1.10e11     2  1101100104 1 [Región d~ 11 [Iquiq~
## # ... with 185,427 more rows, and 642 more variables: comuna <dbl+lbl>,
## #   zona <dbl+lbl>, area <dbl+lbl>, segmento <dbl>, estrato <dbl>,
## #   cod_upm <dbl>, hogar <dbl>, p6_p_con <dbl+lbl>, expr <dbl>, expp <dbl>,
## #   expc <dbl>, varstrat <dbl>, varunit <dbl>, fecha_entrev <date>,
## #   metodologia_entrev <dbl+lbl>, tot_hog <dbl>, numviv <dbl>,
## #   informante_idoneo <dbl+lbl>, tel1 <dbl+lbl>, tel2 <dbl+lbl>,
## #   tel3 <dbl+lbl>, tel4 <dbl+lbl>, tel5 <dbl+lbl>, tel6 <dbl+lbl>,
## #   tel7 <dbl+lbl>, tel8 <dbl+lbl>, p0a <dbl+lbl>, p0b <dbl+lbl>, p1 <dbl+lbl>,
## #   p2 <dbl+lbl>, p3 <dbl+lbl>, p4 <dbl+lbl>, p5 <dbl+lbl>, p6 <dbl>,
## #   p7 <dbl+lbl>, p8 <dbl>, id_persona_e <dbl+lbl>, pco1 <dbl+lbl>,
## #   tot_per <dbl>, h5 <dbl>, ecivil <dbl+lbl>, h5_1 <chr>, h5_2 <dbl>,
## #   nucleo <dbl>, pco2 <dbl+lbl>, numper <dbl>, n_ocupados <dbl>,
## #   n_desocupados <dbl>, n_inactivos <dbl>, conyuge_jh <dbl+lbl>, numnuc <dbl>,
## #   men18c <dbl+lbl>, may60c <dbl+lbl>, tipohogar <dbl+lbl>, e2 <dbl+lbl>,
## #   e5b <dbl+lbl>, e6a <dbl+lbl>, e6b <dbl+lbl>, asiste2 <dbl+lbl>, esc <dbl>,
## #   esc2 <dbl>, educ <dbl+lbl>, o1 <dbl+lbl>, o2 <dbl+lbl>, o3 <dbl+lbl>,
## #   o3b <dbl+lbl>, o4 <dbl+lbl>, o6 <dbl+lbl>, o7 <dbl+lbl>, o7_esp <chr>,
## #   o9a <chr>, o9b <chr>, oficio4_08 <dbl+lbl>, oficio1_08 <dbl+lbl>,
## #   oficio4_88 <dbl+lbl>, oficio1_88 <dbl+lbl>, o15 <dbl+lbl>, o16 <dbl+lbl>,
## #   o17 <dbl+lbl>, o24 <chr>, rama4 <dbl+lbl>, rama1 <dbl+lbl>,
## #   rama4_rev3 <dbl+lbl>, rama1_rev3 <dbl+lbl>, o29 <dbl+lbl>, o30 <dbl+lbl>,
## #   o31 <dbl+lbl>, o32 <dbl+lbl>, o32_esp <chr>, o32b <dbl+lbl>,
## #   o33a <dbl+lbl>, o33b <dbl+lbl>, o34 <dbl+lbl>, o35 <dbl+lbl>,
## #   o36 <dbl+lbl>, activ <dbl+lbl>, activ2 <dbl+lbl>, ocup_inf <dbl+lbl>,
## #   y1_preg <dbl+lbl>, y1 <dbl>, ...
```

#### `select()` con patrones de texto

Podemos seleccionar variables considerando los prefijos, sufijos o partes de *cómo están nombradas las variables*. Independiente de qué tipo de patrón estes buscando, como todo texto y expresión regular en R (y gran parte de los carácteres) este texto debe venir entre **comillas**. Algunas de las funciones que posibilitan este proceso son:

- `starts_with()`: prefijo 
- `ends_with() `:  sufijo
- `contains()` : contiene una cadena de texto literal
- `matches()` : coincide con una expresión regular


```r
select(datos, starts_with("a"), ends_with("preg"))
```

```
## # A tibble: 185,437 x 63
##       area asiste2    activ   activ2 y1_preg y2a_preg y2b_preg y3a_preg y3b_preg
##    <dbl+l> <dbl+l> <dbl+lb> <dbl+lb> <dbl+l> <dbl+lb> <dbl+lb> <dbl+lb> <dbl+lb>
##  1 1 [Urb~ 2 [No ~  1 [Ocu~  1 [Ocu~ NA      NA       NA        NA       NA     
##  2 1 [Urb~ 1 [Asi~ NA       NA       NA      NA       NA        NA       NA     
##  3 1 [Urb~ 1 [Asi~ NA       NA       NA      NA       NA        NA       NA     
##  4 1 [Urb~ 2 [No ~  1 [Ocu~  1 [Ocu~  1 [Sí]  1 [Día~  1 [Hor~   1 [Sí]   1 [Sí]
##  5 1 [Urb~ 2 [No ~  1 [Ocu~  1 [Ocu~ NA      NA       NA        NA       NA     
##  6 1 [Urb~ 2 [No ~  1 [Ocu~  1 [Ocu~  1 [Sí]  1 [Día~  1 [Hor~   2 [No]   2 [No]
##  7 1 [Urb~ 1 [Asi~  3 [Ina~  4 [Ina~ NA      NA       NA        NA       NA     
##  8 1 [Urb~ 2 [No ~  3 [Ina~  4 [Ina~ NA      NA       NA        NA       NA     
##  9 1 [Urb~ 2 [No ~  3 [Ina~  4 [Ina~ NA      NA       NA        NA       NA     
## 10 1 [Urb~ 2 [No ~  3 [Ina~  4 [Ina~ NA      NA       NA        NA       NA     
## # ... with 185,427 more rows, and 54 more variables: y3c_preg <dbl+lbl>,
## #   y3d_preg <dbl+lbl>, y3e_preg <dbl+lbl>, y3f_preg <dbl+lbl>,
## #   y4a_preg <dbl+lbl>, y4b_preg <dbl+lbl>, y4c_preg <dbl+lbl>,
## #   y4d_preg <dbl+lbl>, y5a_preg <dbl+lbl>, y5b_preg <dbl+lbl>,
## #   y5c_preg <dbl+lbl>, y5d_preg <dbl+lbl>, y5e_preg <dbl+lbl>,
## #   y5f_preg <dbl+lbl>, y5g_preg <dbl+lbl>, y5h_preg <dbl+lbl>,
## #   y5i_preg <dbl+lbl>, y5j_preg <dbl+lbl>, y5k_preg <dbl+lbl>,
## #   y5l_preg <dbl+lbl>, y6_preg <dbl+lbl>, y7_preg <dbl+lbl>,
## #   y8_preg <dbl+lbl>, y9_preg <dbl+lbl>, y10_preg <dbl+lbl>,
## #   y11_preg <dbl+lbl>, y12a_preg <dbl+lbl>, y12b_preg <dbl+lbl>,
## #   y13a_preg <dbl+lbl>, y13b_preg <dbl+lbl>, y13c_preg <dbl+lbl>,
## #   y14a_preg <dbl+lbl>, y14b_preg <dbl+lbl>, y14c_preg <dbl+lbl>,
## #   y15a_preg <dbl+lbl>, y15b_preg <dbl+lbl>, y15c_preg <dbl+lbl>,
## #   y16a_preg <dbl+lbl>, y16b_preg <dbl+lbl>, y17_preg <dbl+lbl>,
## #   y18a_preg <dbl+lbl>, y18b_preg <dbl+lbl>, y18c_preg <dbl+lbl>,
## #   y18d_preg <dbl+lbl>, y22_preg <dbl+lbl>, y23a_preg <dbl+lbl>,
## #   y24_preg <dbl+lbl>, y25a_preg <dbl+lbl>, y25g_preg <dbl+lbl>,
## #   y26a_preg <dbl+lbl>, y26b_preg <dbl+lbl>, y26d_preg <dbl+lbl>,
## #   y27_preg <dbl+lbl>, v19_preg <dbl+lbl>
```

```r
# También se pueden combinar con operadores logicos

select(datos, starts_with("y1")&ends_with("preg")) 
```

```
## # A tibble: 185,437 x 21
##     y1_preg y10_preg  y11_preg y12a_preg y12b_preg y13a_preg y13b_preg y13c_preg
##    <dbl+lb> <dbl+lb> <dbl+lbl> <dbl+lbl> <dbl+lbl> <dbl+lbl> <dbl+lbl> <dbl+lbl>
##  1  NA            NA NA           2 [No]    2 [No]    2 [No]    2 [No]    1 [Sí]
##  2  NA            NA NA          NA        NA         2 [No]    1 [Sí]    2 [No]
##  3  NA            NA NA          NA        NA         2 [No]    2 [No]    1 [Sí]
##  4   1 [Sí]       NA NA           2 [No]    2 [No]    2 [No]    2 [No]    2 [No]
##  5  NA            NA NA           2 [No]    2 [No]    2 [No]    2 [No]    1 [Sí]
##  6   1 [Sí]       NA NA           2 [No]    2 [No]    2 [No]    2 [No]    2 [No]
##  7  NA            NA  2 [No r~    2 [No]    2 [No]    2 [No]    2 [No]    2 [No]
##  8  NA            NA  2 [No r~    2 [No]    2 [No]    2 [No]    2 [No]    2 [No]
##  9  NA            NA  2 [No r~    2 [No]    2 [No]    2 [No]    2 [No]    2 [No]
## 10  NA            NA  2 [No r~    2 [No]    2 [No]    2 [No]    2 [No]    2 [No]
## # ... with 185,427 more rows, and 13 more variables: y14a_preg <dbl+lbl>,
## #   y14b_preg <dbl+lbl>, y14c_preg <dbl+lbl>, y15a_preg <dbl+lbl>,
## #   y15b_preg <dbl+lbl>, y15c_preg <dbl+lbl>, y16a_preg <dbl+lbl>,
## #   y16b_preg <dbl+lbl>, y17_preg <dbl+lbl>, y18a_preg <dbl+lbl>,
## #   y18b_preg <dbl+lbl>, y18c_preg <dbl+lbl>, y18d_preg <dbl+lbl>
```

```r
select(datos, contains("pobre")|contains("vivienda"))
```

```
## # A tibble: 185,437 x 3
##          pobreza                          pobreza_sinte id_vivienda
##        <dbl+lbl>                              <dbl+lbl>       <dbl>
##  1 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100101
##  2 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100101
##  3 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100102
##  4 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100102
##  5 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100102
##  6 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100103
##  7 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100103
##  8 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100103
##  9 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100104
## 10 3 [No pobres] 3 [No pobres sin transferencia Covid ]  1101100104
## # ... with 185,427 more rows
```

```r
select(datos, matches("pobreza_|vivienda"))
```

```
## # A tibble: 185,437 x 2
##    id_vivienda                          pobreza_sinte
##          <dbl>                              <dbl+lbl>
##  1  1101100101 3 [No pobres sin transferencia Covid ]
##  2  1101100101 3 [No pobres sin transferencia Covid ]
##  3  1101100102 3 [No pobres sin transferencia Covid ]
##  4  1101100102 3 [No pobres sin transferencia Covid ]
##  5  1101100102 3 [No pobres sin transferencia Covid ]
##  6  1101100103 3 [No pobres sin transferencia Covid ]
##  7  1101100103 3 [No pobres sin transferencia Covid ]
##  8  1101100103 3 [No pobres sin transferencia Covid ]
##  9  1101100104 3 [No pobres sin transferencia Covid ]
## 10  1101100104 3 [No pobres sin transferencia Covid ]
## # ... with 185,427 more rows
```


#### `select()` y condiciones lógicas

Si combinamos `select()` con `where()` obtendremos algo así como una frase *"seleciona donde"*, ese *donde* responde a una condición que cumple cierta variable. Por ejemplo, queremos seleccionar todas las variables que son carácteres (`is.character`):


```r
select(datos, where(is.character))
```

```
## # A tibble: 185,437 x 19
##    h5_1   o7_esp o9a     o9b    o24     o32_esp y3f_esp y4d_esp y18d_esp y27_esp
##    <chr>  <chr>  <chr>   <chr>  <chr>   <chr>   <chr>   <chr>   <chr>    <chr>  
##  1 0      ""     "VENDE~ "VEND~ "VENDE~ ""      ""      ""      ""       ""     
##  2 5      ""     ""      ""     ""      ""      ""      ""      ""       ""     
##  3 32     ""     ""      ""     ""      ""      ""      ""      ""       ""     
##  4 0      ""     "GASTR~ "BARM~ "HOTEL~ ""      ""      ""      ""       ""     
##  5 32     ""     "ARTES~ "QUEQ~ "HACE ~ ""      ""      ""      ""       ""     
##  6 0      ""     "DGAC"  "CONT~ "AEROP~ ""      ""      ""      ""       ""     
##  7 116|1~ ""     ""      ""     ""      ""      ""      ""      ""       ""     
##  8 0      ""     ""      ""     ""      ""      ""      ""      ""       ""     
##  9 0      ""     ""      ""     ""      ""      ""      ""      ""       ""     
## 10 0      ""     ""      ""     ""      ""      ""      ""      ""       ""     
## # ... with 185,427 more rows, and 9 more variables: y28_1j_esp <chr>,
## #   s18_esp <chr>, s28_esp <chr>, s30_esp <chr>, r1b_comuna_esp <chr>,
## #   r1b_pais_esp <chr>, r2_comuna_esp <chr>, r2_pais_esp <chr>, v20_esp <chr>
```

Luego de la revisión del libro de códigos y la exploración de datos mediante a funciones como `find_var()` de `sjmisc` decidimos trabajar con las siguientes variables.

- `edad`
- `sexo`
- `s13`: previsión de salud
- `tot_per`: número de personas en el hogar
- `ytoth`: ingresos totales del hogar
- `o1`: ocupación
- `y26d_total`: Monto del IFE
- `y26d_hog`: ¿Alguien recibió el IFE?

¡Apliquémos conocimientos!


```r
select(datos, edad, sexo, prev =592, ocupacion = o1, tot_per, ytoth, starts_with("y26d_")&matches("total|hog"))
```

```
## # A tibble: 185,437 x 8
##     edad      sexo             prev ocupacion tot_per  ytoth y26d_hog y26d_total
##    <dbl> <dbl+lbl>        <dbl+lbl> <dbl+lbl>   <dbl>  <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 2 [Mujer] 3 [ISAPRE]          2 [No]       2 3.91e5   1 [Sí]         NA
##  2     4 2 [Mujer] 3 [ISAPRE]         NA            2 3.91e5   1 [Sí]         NA
##  3     5 2 [Mujer] 4 [Ninguno (par~   NA            3 9.48e5   2 [No]         NA
##  4    45 1 [Hombr~ 1 [Sistema Públ~    1 [Sí]       3 9.48e5   2 [No]         NA
##  5    19 2 [Mujer] 4 [Ninguno (par~    2 [No]       3 9.48e5   2 [No]         NA
##  6    57 1 [Hombr~ 5 [Otro sistema]    1 [Sí]       3 3.00e6   2 [No]         NA
##  7    20 1 [Hombr~ 5 [Otro sistema]    2 [No]       3 3.00e6   2 [No]         NA
##  8    56 2 [Mujer] 5 [Otro sistema]    2 [No]       3 3.00e6   2 [No]         NA
##  9    77 1 [Hombr~ 1 [Sistema Públ~    2 [No]       2 6.10e5   2 [No]         NA
## 10    60 2 [Mujer] 1 [Sistema Públ~    2 [No]       2 6.10e5   2 [No]         NA
## # ... with 185,427 more rows
```

Es una buena práctica trabajar solo con las columnas que utilizaremos para el análisis, principalmente pues disminuye el *uso de memoria*


```r
datos_proc <- select(datos, edad, sexo, prev = 592, ocupacion = o1, tot_per, ytoth, starts_with("y26d_")&matches("total|hog"))
```

El nuevo objeto posee650 variables (columnas), pero conserva las filas 185.437 (u observaciones) ¿Qué pasa si quiero trabajar con un *subset* de casos? La respuesta es `filter()`

### 5.2 `filter()` para manipular observaciones

La función `filter()` de `dplyr` escoge o extrae filas basados en sus valores, subdivide un data frame (*subset*), reteniendo todas las filas que satisfacen sus condiciones.

Con `filter()` será esencial el uso de los **operadores** que ya vimos, dado que las observaciones que preservarán en nuestros datos (y aquellas que no), están definidas por condiciones lógicas (relacionales o booleanas)

{{< div "note" >}}
filter(datos, condicion_para filtrar)
Esta condición para filtrar podría ser, por ejemplo
variable1 >= 3
{{< /div >}}

#### `filter` con números

Imaginémos que queremos una base con las personas mayores de 15 años. Pero también que pertenezcan a hogares con menos de 7 personas. 

```r
filter(datos_proc, edad >= 15)
```

```
## # A tibble: 151,315 x 8
##     edad      sexo             prev ocupacion tot_per  ytoth y26d_hog y26d_total
##    <dbl> <dbl+lbl>        <dbl+lbl> <dbl+lbl>   <dbl>  <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 2 [Mujer] 3 [ISAPRE]          2 [No]       2 3.91e5   1 [Sí]         NA
##  2    45 1 [Hombr~ 1 [Sistema Públ~    1 [Sí]       3 9.48e5   2 [No]         NA
##  3    19 2 [Mujer] 4 [Ninguno (par~    2 [No]       3 9.48e5   2 [No]         NA
##  4    57 1 [Hombr~ 5 [Otro sistema]    1 [Sí]       3 3.00e6   2 [No]         NA
##  5    20 1 [Hombr~ 5 [Otro sistema]    2 [No]       3 3.00e6   2 [No]         NA
##  6    56 2 [Mujer] 5 [Otro sistema]    2 [No]       3 3.00e6   2 [No]         NA
##  7    77 1 [Hombr~ 1 [Sistema Públ~    2 [No]       2 6.10e5   2 [No]         NA
##  8    60 2 [Mujer] 1 [Sistema Públ~    2 [No]       2 6.10e5   2 [No]         NA
##  9    54 2 [Mujer] 1 [Sistema Públ~    1 [Sí]       4 1.32e6   1 [Sí]         NA
## 10    18 1 [Hombr~ 4 [Ninguno (par~    2 [No]       4 1.32e6   1 [Sí]         NA
## # ... with 151,305 more rows
```

```r
filter(datos_proc, edad >= 15 & tot_per <7)
```

```
## # A tibble: 144,418 x 8
##     edad      sexo             prev ocupacion tot_per  ytoth y26d_hog y26d_total
##    <dbl> <dbl+lbl>        <dbl+lbl> <dbl+lbl>   <dbl>  <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 2 [Mujer] 3 [ISAPRE]          2 [No]       2 3.91e5   1 [Sí]         NA
##  2    45 1 [Hombr~ 1 [Sistema Públ~    1 [Sí]       3 9.48e5   2 [No]         NA
##  3    19 2 [Mujer] 4 [Ninguno (par~    2 [No]       3 9.48e5   2 [No]         NA
##  4    57 1 [Hombr~ 5 [Otro sistema]    1 [Sí]       3 3.00e6   2 [No]         NA
##  5    20 1 [Hombr~ 5 [Otro sistema]    2 [No]       3 3.00e6   2 [No]         NA
##  6    56 2 [Mujer] 5 [Otro sistema]    2 [No]       3 3.00e6   2 [No]         NA
##  7    77 1 [Hombr~ 1 [Sistema Públ~    2 [No]       2 6.10e5   2 [No]         NA
##  8    60 2 [Mujer] 1 [Sistema Públ~    2 [No]       2 6.10e5   2 [No]         NA
##  9    54 2 [Mujer] 1 [Sistema Públ~    1 [Sí]       4 1.32e6   1 [Sí]         NA
## 10    18 1 [Hombr~ 4 [Ninguno (par~    2 [No]       4 1.32e6   1 [Sí]         NA
## # ... with 144,408 more rows
```

¿Y si quiero filtrar para saber el valor máximo de ingresos (`ytoth`)?


```r
filter(datos_proc, ytoth == max(ytoth))
```

```
## # A tibble: 1 x 8
##    edad      sexo             prev ocupacion tot_per   ytoth y26d_hog y26d_total
##   <dbl> <dbl+lbl>        <dbl+lbl> <dbl+lbl>   <dbl>   <dbl> <dbl+lb>  <dbl+lbl>
## 1    41 1 [Hombr~ 1 [Sistema Públ~    1 [Sí]       1  2.25e8   2 [No]         NA
```

¡Gana \$225.200.000, es Hombre y tiene 41 años! (y vive solo...)


#### `filter()` con carácteres

Si queremos filtrar por la variable `sexo` solo a las mujeres, tengo dos opciones: o solo selecciono a las mujeres (`==`) o excluyo a los hombres (`!=`).

Ahora bien, antes hay que hacer una precisión importante: en los datos `sexo` es una variable que está como `dbl` y `lbl` (número etiquetado), por lo que no es que en la base aparezcan "Mujeres" y "Hombres", sino que 2 y 1.

Por ello, con el siguiente código aparecerá un error en sus consolas. 

```r
filter(datos_proc, sexo == "Mujer")
```

Una función **muy muy útil** (sobre todo cuando trabajemos con regresiones) es `as_factor()` que permite conservar los niveles pero definiendo sus categorías de respuesta en base a la etiqueta que traen (el `lbl`)


```r
datos_proc$sexo <- as_factor(datos_proc$sexo)
```

¡Ahora si funcionará!

```r
filter(datos_proc, sexo == "Mujer")
```

```
## # A tibble: 99,341 x 8
##     edad sexo                 prev ocupacion tot_per   ytoth y26d_hog y26d_total
##    <dbl> <fct>           <dbl+lbl> <dbl+lbl>   <dbl>   <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 Mujer 3 [ISAPRE]             2 [No]       2  390833   1 [Sí]         NA
##  2     4 Mujer 3 [ISAPRE]            NA            2  390833   1 [Sí]         NA
##  3     5 Mujer 4 [Ninguno (partic~   NA            3  947583   2 [No]         NA
##  4    19 Mujer 4 [Ninguno (partic~    2 [No]       3  947583   2 [No]         NA
##  5    56 Mujer 5 [Otro sistema]       2 [No]       3 3004167   2 [No]         NA
##  6    60 Mujer 1 [Sistema Público~    2 [No]       2  610250   2 [No]         NA
##  7    54 Mujer 1 [Sistema Público~    1 [Sí]       4 1321481   1 [Sí]         NA
##  8    31 Mujer 1 [Sistema Público~    1 [Sí]       4 1110000   2 [No]         NA
##  9     9 Mujer 1 [Sistema Público~   NA            4 1110000   2 [No]         NA
## 10    77 Mujer 1 [Sistema Público~    2 [No]       1  739833   2 [No]         NA
## # ... with 99,331 more rows
```

```r
filter(datos_proc, sexo != "Hombre")
```

```
## # A tibble: 99,341 x 8
##     edad sexo                 prev ocupacion tot_per   ytoth y26d_hog y26d_total
##    <dbl> <fct>           <dbl+lbl> <dbl+lbl>   <dbl>   <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 Mujer 3 [ISAPRE]             2 [No]       2  390833   1 [Sí]         NA
##  2     4 Mujer 3 [ISAPRE]            NA            2  390833   1 [Sí]         NA
##  3     5 Mujer 4 [Ninguno (partic~   NA            3  947583   2 [No]         NA
##  4    19 Mujer 4 [Ninguno (partic~    2 [No]       3  947583   2 [No]         NA
##  5    56 Mujer 5 [Otro sistema]       2 [No]       3 3004167   2 [No]         NA
##  6    60 Mujer 1 [Sistema Público~    2 [No]       2  610250   2 [No]         NA
##  7    54 Mujer 1 [Sistema Público~    1 [Sí]       4 1321481   1 [Sí]         NA
##  8    31 Mujer 1 [Sistema Público~    1 [Sí]       4 1110000   2 [No]         NA
##  9     9 Mujer 1 [Sistema Público~   NA            4 1110000   2 [No]         NA
## 10    77 Mujer 1 [Sistema Público~    2 [No]       1  739833   2 [No]         NA
## # ... with 99,331 more rows
```

{{< div "note" >}}
**Ojo**. R es *sensible* a cómo está escrito el texto. Si pones el mismo código pero sin respetar mayúsuculas y minúsculas el código no funcionará
{{< /div >}}

¡Por último! ¿Cómo se seleccionan dos condiciones en carácter? Con el operador `%in%`


```r
datos_proc$prev <- as_factor(datos_proc$prev)

filter(datos_proc, prev %in% c("Sistema Público FONASA", "ISAPRE"))
```

```
## # A tibble: 169,503 x 8
##     edad sexo   prev               ocupacion tot_per   ytoth y26d_hog y26d_total
##    <dbl> <fct>  <fct>              <dbl+lbl>   <dbl>   <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 Mujer  ISAPRE                2 [No]       2  390833   1 [Sí]         NA
##  2     4 Mujer  ISAPRE               NA            2  390833   1 [Sí]         NA
##  3    45 Hombre Sistema Público F~    1 [Sí]       3  947583   2 [No]         NA
##  4    77 Hombre Sistema Público F~    2 [No]       2  610250   2 [No]         NA
##  5    60 Mujer  Sistema Público F~    2 [No]       2  610250   2 [No]         NA
##  6    11 Hombre Sistema Público F~   NA            4 1321481   1 [Sí]         NA
##  7    54 Mujer  Sistema Público F~    1 [Sí]       4 1321481   1 [Sí]         NA
##  8    57 Hombre Sistema Público F~    1 [Sí]       4 1321481   1 [Sí]         NA
##  9    55 Hombre Sistema Público F~    1 [Sí]       4 1110000   2 [No]         NA
## 10    31 Mujer  Sistema Público F~    1 [Sí]       4 1110000   2 [No]         NA
## # ... with 169,493 more rows
```

Antes de definir que observaciones vamos a conservar en una base procesada `datos_proc` ¡Creemos transformemos variables con `mutate()`!

### 5.3 `mutate()` para transformación de  variables

La función de `mutate()` permite hacer operaciones para crear nuevas variables o transformar las ya existentes. 

{{< div "note" >}}
mutate(datos, nueva_variable = cálculo o condición)
{{< /div >}}

#### `mutate()` en base a cálculo

Calcularemos una nueva variable llamada `nueva_variable` que proviene de la suma de 2+3. También una variable `ingreso_percapita` que proviene de la división del ingreso total del hogar y el número de personas que residen en el hogar 


```r
mutate(datos_proc, nueva_variable = 3+2)
```

```
## # A tibble: 185,437 x 9
##     edad sexo  prev  ocupacion tot_per  ytoth y26d_hog y26d_total nueva_variable
##    <dbl> <fct> <fct> <dbl+lbl>   <dbl>  <dbl> <dbl+lb>  <dbl+lbl>          <dbl>
##  1    34 Mujer ISAP~    2 [No]       2 3.91e5   1 [Sí]         NA              5
##  2     4 Mujer ISAP~   NA            2 3.91e5   1 [Sí]         NA              5
##  3     5 Mujer Ning~   NA            3 9.48e5   2 [No]         NA              5
##  4    45 Homb~ Sist~    1 [Sí]       3 9.48e5   2 [No]         NA              5
##  5    19 Mujer Ning~    2 [No]       3 9.48e5   2 [No]         NA              5
##  6    57 Homb~ Otro~    1 [Sí]       3 3.00e6   2 [No]         NA              5
##  7    20 Homb~ Otro~    2 [No]       3 3.00e6   2 [No]         NA              5
##  8    56 Mujer Otro~    2 [No]       3 3.00e6   2 [No]         NA              5
##  9    77 Homb~ Sist~    2 [No]       2 6.10e5   2 [No]         NA              5
## 10    60 Mujer Sist~    2 [No]       2 6.10e5   2 [No]         NA              5
## # ... with 185,427 more rows
```

```r
mutate(datos_proc, nueva_variable = 3+2,
       ingreso_percapita = ytoth/tot_per)
```

```
## # A tibble: 185,437 x 10
##     edad sexo  prev  ocupacion tot_per  ytoth y26d_hog y26d_total nueva_variable
##    <dbl> <fct> <fct> <dbl+lbl>   <dbl>  <dbl> <dbl+lb>  <dbl+lbl>          <dbl>
##  1    34 Mujer ISAP~    2 [No]       2 3.91e5   1 [Sí]         NA              5
##  2     4 Mujer ISAP~   NA            2 3.91e5   1 [Sí]         NA              5
##  3     5 Mujer Ning~   NA            3 9.48e5   2 [No]         NA              5
##  4    45 Homb~ Sist~    1 [Sí]       3 9.48e5   2 [No]         NA              5
##  5    19 Mujer Ning~    2 [No]       3 9.48e5   2 [No]         NA              5
##  6    57 Homb~ Otro~    1 [Sí]       3 3.00e6   2 [No]         NA              5
##  7    20 Homb~ Otro~    2 [No]       3 3.00e6   2 [No]         NA              5
##  8    56 Mujer Otro~    2 [No]       3 3.00e6   2 [No]         NA              5
##  9    77 Homb~ Sist~    2 [No]       2 6.10e5   2 [No]         NA              5
## 10    60 Mujer Sist~    2 [No]       2 6.10e5   2 [No]         NA              5
## # ... with 185,427 more rows, and 1 more variable: ingreso_percapita <dbl>
```

¿Qué pasa si queremos, luego de calcular nuestras nuevas variables, filtrar un ingreso per cápita menor o igual a $1.000.000

**¡Ahora entra en escena nuestro operador estrella ` %>% `!**

{{< div "note" >}}
datos %>% 
  mutate(., nueva_variable = calculo ) %>% 
  filter(., nueva_variable <= valor)
{{< /div >}}

Básicamente, el ` %>% ` permite "ingresar" nuestra base de datos como argumento para cada función e ir operándola en proceso


```r
datos_proc %>%
  mutate(ingreso_percapita = ytoth/tot_per) %>% 
  filter(ingreso_percapita <= 1000000)
```

```
## # A tibble: 176,094 x 9
##     edad sexo   prev               ocupacion tot_per   ytoth y26d_hog y26d_total
##    <dbl> <fct>  <fct>              <dbl+lbl>   <dbl>   <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 Mujer  ISAPRE                2 [No]       2  390833   1 [Sí]         NA
##  2     4 Mujer  ISAPRE               NA            2  390833   1 [Sí]         NA
##  3     5 Mujer  Ninguno (particul~   NA            3  947583   2 [No]         NA
##  4    45 Hombre Sistema Público F~    1 [Sí]       3  947583   2 [No]         NA
##  5    19 Mujer  Ninguno (particul~    2 [No]       3  947583   2 [No]         NA
##  6    77 Hombre Sistema Público F~    2 [No]       2  610250   2 [No]         NA
##  7    60 Mujer  Sistema Público F~    2 [No]       2  610250   2 [No]         NA
##  8    11 Hombre Sistema Público F~   NA            4 1321481   1 [Sí]         NA
##  9    54 Mujer  Sistema Público F~    1 [Sí]       4 1321481   1 [Sí]         NA
## 10    18 Hombre Ninguno (particul~    2 [No]       4 1321481   1 [Sí]         NA
## # ... with 176,084 more rows, and 1 more variable: ingreso_percapita <dbl>
```

#### `recode()`

La función denominada *recode* puede reemplazar valores numéricos en base a su posición o su nombre, y valores de carácteres o factores sólo por su nombre. 

En el siguiente ejemplo recodificamos las categorías de respuesta de Mujer a Femenino y de Hombre a Masculino


```r
datos_proc %>% 
  mutate(sexo = dplyr::recode(sexo, "Mujer" = "Femenino", "Hombre" = "Masculino"))
```

```
## # A tibble: 185,437 x 8
##     edad sexo     prev              ocupacion tot_per  ytoth y26d_hog y26d_total
##    <dbl> <fct>    <fct>             <dbl+lbl>   <dbl>  <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 Femenino ISAPRE               2 [No]       2 3.91e5   1 [Sí]         NA
##  2     4 Femenino ISAPRE              NA            2 3.91e5   1 [Sí]         NA
##  3     5 Femenino Ninguno (particu~   NA            3 9.48e5   2 [No]         NA
##  4    45 Masculi~ Sistema Público ~    1 [Sí]       3 9.48e5   2 [No]         NA
##  5    19 Femenino Ninguno (particu~    2 [No]       3 9.48e5   2 [No]         NA
##  6    57 Masculi~ Otro sistema         1 [Sí]       3 3.00e6   2 [No]         NA
##  7    20 Masculi~ Otro sistema         2 [No]       3 3.00e6   2 [No]         NA
##  8    56 Femenino Otro sistema         2 [No]       3 3.00e6   2 [No]         NA
##  9    77 Masculi~ Sistema Público ~    2 [No]       2 6.10e5   2 [No]         NA
## 10    60 Femenino Sistema Público ~    2 [No]       2 6.10e5   2 [No]         NA
## # ... with 185,427 more rows
```

El problema de `recode()` que se utiliza dentro de `dplyr` es que si recodifico se pierde la etiqueta anterior. Esto es un problema a si por ejemplo, solo quiero recodificar casos perdidos. 

Frente a ello, con el tiempo nos hemos convencido de que la mejor solución hasta ahora es ocupar `recode()` del paquete `car`. Si recuerdan, dos funciones con el mismo nombre podrían producir *conflictos*, y por ello, especificaremos con `car::recode()` que la función `recode()` que ocupamos proviene de `car`

{{< div "note" >}}
datos %$% 
 car::recode(.$variable, c('valor_orig1=nuevo_valor1;valor_org2=nuevo_valor2'))
 
*Ojo: %$% es el primo hermano de %>% (básicamente funcionan igual, pero este es necesario para car)*
{{< /div >}}


```r
datos_proc %$% 
  car::recode(.$y26d_hog, c('9=NA')) %>% head(.)
```

```
## <labelled<double>[6]>: y26d_hog. Últimos 12 meses, ¿alguien recibió Ingreso Familiar de Emergencia?
## [1] 1 1 2 2 2 2
## 
## Labels:
##  value   label
##      1      Sí
##      2      No
##      9 No sabe
```

Aquí una versión de si la recodificación es hacia carácteres (mismo ejemplo que con `recode()` de `dplyr`)


```r
datos_proc %$% 
  car::recode(.$sexo, c('"Mujer"="Femenino";"Hombre"= "Masculino"'), as.factor = T) %>% head(.)
```

```
## [1] Femenino  Femenino  Femenino  Masculino Femenino  Masculino
## Levels: Femenino Masculino
```


### 5.3.2 `if_else()` para construir variables condicionales

La función `if_else()` permite construir variables en base a condiciones lógicas. Su estructura es la siguiente

{{< div "note" >}}
if_else(condición,TRUE,FALSE)
Donde dice `TRUE` es el valor que se obtiene si la condición es verdadera, mientras que `FALSE` es todo el resto de las opciones (o cuando es FALSA)
{{< /div >}}

Crearemos una variable que *dummy* que indica si el respondente es *FONASA* o no lo es. 


```r
datos_proc %>% 
 		 mutate(fonasa = if_else(prev == "Sistema Público FONASA", 1, 0))
```

También podemos ocupar esta función como validador, por ejemplo, rellenando con valores lógicos como `FALSE` cuando no hay valores en `ytoth`. Luego esos `FALSE` podrían ser contados en otros procesos estadísticos


```r
datos_proc %>% 
  mutate(validador_ingreso = if_else(is.na(ytoth), FALSE, TRUE))
```

```
## # A tibble: 185,437 x 9
##     edad sexo   prev               ocupacion tot_per   ytoth y26d_hog y26d_total
##    <dbl> <fct>  <fct>              <dbl+lbl>   <dbl>   <dbl> <dbl+lb>  <dbl+lbl>
##  1    34 Mujer  ISAPRE                2 [No]       2  390833   1 [Sí]         NA
##  2     4 Mujer  ISAPRE               NA            2  390833   1 [Sí]         NA
##  3     5 Mujer  Ninguno (particul~   NA            3  947583   2 [No]         NA
##  4    45 Hombre Sistema Público F~    1 [Sí]       3  947583   2 [No]         NA
##  5    19 Mujer  Ninguno (particul~    2 [No]       3  947583   2 [No]         NA
##  6    57 Hombre Otro sistema          1 [Sí]       3 3004167   2 [No]         NA
##  7    20 Hombre Otro sistema          2 [No]       3 3004167   2 [No]         NA
##  8    56 Mujer  Otro sistema          2 [No]       3 3004167   2 [No]         NA
##  9    77 Hombre Sistema Público F~    2 [No]       2  610250   2 [No]         NA
## 10    60 Mujer  Sistema Público F~    2 [No]       2  610250   2 [No]         NA
## # ... with 185,427 more rows, and 1 more variable: validador_ingreso <lgl>
```

### 5.3.2 `case_when()` para construir variable en base a múltiples condiciones

Una función que se utiliza frecuentemente para *colapsar* categorías o construir categorías en base a varias condiciones es `case_when()` por lo lógico y *fácil* que es de entender

{{< div "note" >}}
case_when(variable == condicion ~ valor1,
          variable == condicion ~ valor2,
          TRUE ~ NA_real)
- Donde, TRUE indica "todo el resto", y el NA dependerá de la clase del valor de recodificación
{{< /div >}}

Un ejemplo claro es cuando queremos construir *categorías de edad*


```r
datos_proc %>% 
  mutate(edad_tramo = case_when(edad <=39 ~  "Joven",
                                edad > 39 & edad <=59 ~ "Adulto",
                                edad > 59 ~ "Adulto mayor",
                                TRUE ~ NA_character_)) %>% 
  select(edad, edad_tramo)
```

```
## # A tibble: 185,437 x 2
##     edad edad_tramo  
##    <dbl> <chr>       
##  1    34 Joven       
##  2     4 Joven       
##  3     5 Joven       
##  4    45 Adulto      
##  5    19 Joven       
##  6    57 Adulto      
##  7    20 Joven       
##  8    56 Adulto      
##  9    77 Adulto mayor
## 10    60 Adulto mayor
## # ... with 185,427 more rows
```

Como se puede ver, no solamente indicamos tramos de la variable edad, sino que utilizamos operadores lógicos (`&`). Podríamos ocupar el que necesitemos, y sobre todo, también combinar variables (por ejemplo, crear una variable `sexo-edad`)

## 6. Resumen con procesamiento de las variables

Hasta ahora, solo hemos creado una base de datos que selecciona variables. Ahora nos resta incorporar en un nuevo objeto los cambios que nos parezcan relevantes para la base de datos procesada que utilizaremos en nuestros análisis. 

Como ya conocemos operadores que permiten concatenar procesos ( `%>%`  y `%$%`) este procedimiento será mucho más fácil. 


```r
datos_proc %>% 
 filter(edad >= 15 & tot_per <7) %>%
    mutate(ingreso_percapita = ytoth/tot_per,
           edad_tramo = case_when(edad <=39 ~  "Joven",
                                edad > 39 & edad <=59 ~ "Adulto",
                                edad > 59 ~ "Adulto mayor",
                                TRUE ~ NA_character_),
           fonasa = if_else(prev == "Sistema Público FONASA", 1, 0),
           ocupacion = as_factor(ocupacion)) %>%
  select(sexo, edad_tramo, ocupacion, ingreso_percapita, ife = y26d_hog)
```

```
## # A tibble: 144,418 x 5
##    sexo   edad_tramo   ocupacion ingreso_percapita       ife
##    <fct>  <chr>        <fct>                 <dbl> <dbl+lbl>
##  1 Mujer  Joven        No                  195416.    1 [Sí]
##  2 Hombre Adulto       Sí                  315861     2 [No]
##  3 Mujer  Joven        No                  315861     2 [No]
##  4 Hombre Adulto       Sí                 1001389     2 [No]
##  5 Hombre Joven        No                 1001389     2 [No]
##  6 Mujer  Adulto       No                 1001389     2 [No]
##  7 Hombre Adulto mayor No                  305125     2 [No]
##  8 Mujer  Adulto mayor No                  305125     2 [No]
##  9 Mujer  Adulto       Sí                  330370.    1 [Sí]
## 10 Hombre Joven        No                  330370.    1 [Sí]
## # ... with 144,408 more rows
```

¡Ahora que estamos seguras/os sobre-escribimos la base!

```r
datos_proc <- datos_proc %>% 
 filter(edad >= 15 & tot_per <7) %>%
    mutate(ingreso_percapita = ytoth/tot_per,
           edad_tramo = case_when(edad <=39 ~  "Joven",
                                edad > 39 & edad <=59 ~ "Adulto",
                                edad > 59 ~ "Adulto mayor",
                                TRUE ~ NA_character_),
           fonasa = if_else(prev == "Sistema Público FONASA", 1, 0),
           ocupacion = as_factor(ocupacion)) %>%
  select(sexo, edad_tramo, ocupacion, ingreso_percapita, ife = y26d_hog)
```

Podemos visualizar la base resultante a partir de `view_df()` de `sjPlot`

```r
sjPlot::view_df(datos_proc)
```

<table style="border-collapse:collapse; border:none;">
<caption>Data frame: datos_proc</caption>
<tr>
<th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">ID</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Name</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Label</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Values</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Value Labels</th>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
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
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ingreso_percapita</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingreso total del hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-225200000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ife</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">y26d_hog. Ãšltimos 12 meses, Â¿alguien recibiÃ³<br>Ingreso Familiar de Emergencia?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">SÃ­<br>No<br>No sabe</td>
</tr>

</table>

## 7. Guardar base procesada

Para guardar la base de datos procesada, debes dirigir la ruta hacia tu Rproject


```r
saveRDS(datos_proc, file = "../nombre_project/output/datos_proc.rds")
```

## 8. Reporte de progreso
  
¡Recuerda rellenar tu [reporte de progreso](https://learn-r.formr.org/). En tu correo electrónico está disponible el código mediante al cuál debes acceder para actualizar tu estado de avance del curso.

