---
title: "Bienvenido/a a R, RStudio e instalar paquetes"
linktitle: "1: R, RStudio e instalar paquetes"
date: "2021-08-13"
menu:
  example:
    parent: Ejemplos
    weight: 1
type: docs
toc: true
editor_options: 
  chunk_output_type: console
---

# Objetivo de la práctica 

Introducir en aspectos relativos a R, RStudio e instalación de paquetes. El práctico considera los contenidos profundizados en el [bloque 1](/content/01-content), que serán repasados en tres partes: 1. RStudio, 2. Rprojects, 3. Script (.R) y 4. Instalar paquetes

## Parte 1: Empenzando a asimilarnos con RStudio 

RStudio es un software que permite integrar diferentes herramientas asociadas a R (IDE), de modo de hacer más fácil tu interacción con el. Este programa está orientado en dar una interfaz intuitiva para el análisis estadístico [^1].

Por ello podrás encontrar los siguientes elementos en esta

1. **Consola**: donde puedes escribir código (input) y donde recibirás resultados (output). Lo que se escriba en esa consola, independiente de su está bien o mal quedará registrado en el caché (`.RHistory`). Otro lugar donde podrás ver resultados es en *Plots* y *Viewer*.

2. **Script**: es un documento de extensión *.R* donde se alojan los códigos que permiten ejecutar los análisis que buscas realizar. Además de contener **código ejecutable**, los códigos que se guarden son los que realmente quieres guardar (y no los mil intentos que hacemos previamente).

3. **Enviroment**: es una forma de interactuar con el espacio de trabajo **dentro de R** (*Workspace*) pues podremos visualizar los diferentes **objetos** que vamos creando para ser manipulados, junto con los **paquetes** que tenemos cargados.

4. **Files**: es una forma de visualizar el espacio de trabajo en el que nos "posicionamos" **fuera de R** (*Working directory*). Por ejemplo, con los archivos que cargamos para trabajar en nuestro RStudio (como los datos). Si estos no están en el espacio de trabajo en que estamos, probablemente estaremos en un problema (¡ya les mostraremos la solución!)

5. **Help**: un amigo/a fiel. Nos dará ayuda e información útil del programa y los paquetes. Otros lugares donde encontrar ayuda es en *Tutorial* y en *Packages*. El primero ofrece tutoriales, como dice su nombre, y el segundo no solo nos indica qué paquetes tenemos instalados y/o cargados, sino que también **dónde encontrar más documentación sobre estos** (en el ícono del planeta).

[^1]: Aquí luego aparecerá la parte del video que tiene que ver con **RStudio**

## Parte 2: RStudio Projects

Uno de los aspectos **más potentes** y **útiles** de RStudio es su capacidad de manejar proyectos. 

La primera vez que tu abres R este está "posicionado" en alguna carpeta de tu computadora (*¿cuál? una buena pregunta...*) y todo lo que se haga se hará relativo a esa carpeta. El concepto "técnico" de eso es lo que ya les había comentado: el *Working Directory*.

¿Cómo saber dónde estoy?

1. Puedes mirar arriba de la consola y aparecerá tu versión de R y la ruta `.~` [^2]

<img src="/img/example/working-directory.png" width="50%" />

2. Escribiendo en tu consola `getwd()` (obteniendo mi working directory) y ver el resultado de esta

En la pestaña `Files` podrán ver qué archivos están en el ambiente del *Working directory*

Sea cual sea la forma de ver el directorio de trabajo notaremos desde ya que es algo "críptico". Muchas veces tendremos que transformarnos en detectives de *rutas* del directorio, sobre todo cuando tenemos **millones de carpetas** y no tenemos una organización clara de dónde tenemos nuestros códigos y datos. 

[^2]: El signo `~` (tilde) indica en Windows `C:\Users\your_user_name\` y en macOS `/Users/your_user_name/`.

En ese ejercicio intentaremos cambiar nuestro directorio de trabajo (*Set Working Directory*) para poder alojar nuestro directorio donde están nuestros archivos necesarios para el análisis. Eso lo tendríamos que hacer **cada vez que abrimos el programa** (en R terminal se hace manualmente y en RStudio se puede guardar ese código en el script).

{{< div "note" >}}
Para ello debemos utilizar `setwd()` y dentro de esta indicar la ruta y dar vuelta las barras (`\`) por (`/`).
{{< /div >}}

Como mencionámos, parte importante de los scripts tienen al inicio un directorio del estilo `setwd("C:\\Users\\bill\\Desktop\\Important research project")` lo que es una **pésima idea** [(para más explicación véase aquí)](https://www.tidyverse.org/blog/2017/12/workflow-vs-script/)

1. Si corres ese directorio a algún otro lugar o corres el **script en otro computador** o **compartes con otra persona** la **ruta estará mala y nada funcionará**.

La mejor manera de manejar los directorios de trabajo (*Working Directory*) con *RStudio* es utilizar los **RProjects** de RStudio.

**¿Qué es un RProject?**

Estos son archivos especiales que RStudio crea y que terminan en una extensión `.Rproj`.Cuando abra uno de estos archivos especiales, se abrirá una nueva sesión de RStudio y se apuntará al directorio correcto automáticamente. Si mueves la carpeta más tarde o la abres en un computador diferente, funcionará sin problemas y no lloraremos por el problema del directorio de trabajo

En general, puedes crear un nuevo proyecto yendo a `File > New Project > New Directory > Empty Project/Exting directory`, que creará una nueva carpeta en tu ordenador que está vacía excepto por un único archivo `.Rproj`. Haga doble clic en ese archivo para abrir una sesión de RStudio que apunte a la carpeta correcta.

Como vimos en la sesión, los *RProject* potenciarán nuestra posibilidad de colaborar con otros/as usuarias/os de R. No olvides que además, nos permitirá trabajar con rutas relativas de manera interna, dando la posibilidad de mantener ordenada la información de nuestros proyectos. 

- <i class="fab fa-r-project"></i>[Descargar Rproject del bloque 1 `01-bloque.zip`](https://github.com/learn-R/02-class/raw/main/01-bloque.zip)

{{< div "note" >}}
Descargarás un archivo .zip, para ver como descomprimir un archivom en caso de que no sepas cómo [revisa esta breve guía](/resource/unzipping/)
{{< /div >}}



## Parte 3: Partes de un script (.R)


```r
# Los gatos ( # ) son comentarios
print("¡Hola estudiantes!")
```

```
## [1] "¡Hola estudiantes!"
```

```r
# Lo que está antes de los paréntesis son las funciones
# Lo que está dentro de los paréntesis son argumentos
```

### Algunos shortcuts importantes

Los siguientes atajos del teclado serán sus mejores amigos/as

- **Ejecutar** líneas de código del script:   <kbd>CTRL</kbd> <kbd>Enter</kbd>

- **Guardar** el script (archivo . R):   <kbd>CTRL</kbd> <kbd>S</kbd>

- **Nuevo** script (archivo . R):   <kbd>CTRL</kbd> <kbd>N</kbd>

- *Título automático* dentro del script: <kbd>CTRL</kbd> <kbd>Shift</kbd> <kbd>R</kbd>


{{< div "note" >}}
*¿Qué pasa si me aparece el script con símbolos raros?* En la barra del navegador de tu *RStudio* debes buscar:

File → Reopen with Encoding → seleccionar `UTF-8`
{{< /div >}}

<img src="/img/example/01/orden-sintaxis.png" width="70%" />

### Elementos del lenguaje de R 

**Calculando una media**

```r
(25 + 24 + 22)/3
```

```
## [1] 23.66667
```

```r
media <- (25 + 24 + 22)/3
media
```

```
## [1] 23.66667
```

**Carácteres** (`character`)

```r
c("Valentina", "Nicolás", "Dafne")
```

```
## [1] "Valentina" "Nicolás"   "Dafne"
```

```r
equipo <- c("Valentina", "Nicolás", "Dafne")
```

**Números** (`numeric`)

```r
c(25, 22, 24)
```

```
## [1] 25 22 24
```

```r
edad <- c(25,22,24)
mean(edad)
```

```
## [1] 23.66667
```

**Lonitudes** (`integer`)

```r
1:3
```

```
## [1] 1 2 3
```

```r
orden <- 1:3
```

**Tabulados**[^1] (`data.frame`)

```r
?data.frame()
```


```r
datos <- data.frame(equipo, edad, orden)
```

[^1] O base de datos (pese a que **realmente una tabla no es una base de datos**)

Y ahora **¿cómo calculamos la media?**

```r
mean(datos$edad)
```

```
## [1] 23.66667
```

## Parte 4: Instalar paquetes

Muchas veces requeriremos de extender los potenciales de `R base`. Para implementar estas extensiones será necesario realizar un proceso de carga de paquetes (también llamadas librarías) que contienen funciones muy útiles. Este proceso de compone de dos partes esenciales (1) Instalar paquetes (`ìnstall.packages()`) y (2) Llamar paquetes (`library()`). Veamos su paso a paso

**0. Información de la sesión**

No es un paso que debas hacer siempre. Pero lo realizaremos por esta vez para que véas que con este comando podremos obtener información útil sobre el ambiente en el que estas desarrollando tus análisis. Por la función `sessionInfo()` en tu consola y ve que nos arroja. Probablemente la ocuparás cuando tengas problemas con un código y requieras de ayuda. 


```r
sessionInfo()
```

```
## R version 4.0.4 (2021-02-15)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## Running under: Windows 10 x64 (build 19043)
## 
## Matrix products: default
## 
## locale:
## [1] LC_COLLATE=Spanish_Chile.1252  LC_CTYPE=Spanish_Chile.1252   
## [3] LC_MONETARY=Spanish_Chile.1252 LC_NUMERIC=C                  
## [5] LC_TIME=Spanish_Chile.1252    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## loaded via a namespace (and not attached):
##  [1] bookdown_0.21     digest_0.6.27     R6_2.5.0          jsonlite_1.7.2   
##  [5] magrittr_2.0.1    evaluate_0.14     highr_0.8         blogdown_1.3     
##  [9] stringi_1.5.3     rlang_0.4.10      jquerylib_0.1.3   bslib_0.2.4      
## [13] rmarkdown_2.7     tools_4.0.4       stringr_1.4.0     xfun_0.22        
## [17] yaml_2.2.1        compiler_4.0.4    htmltools_0.5.1.1 knitr_1.31       
## [21] sass_0.3.1
```

**1. Instalar paquetes**

Para instalar paquetes utilizaremos `install.packages()`. Dentro del `()` tenemos que rellenar cuál paquete queremos instalar. Pero ... una pregunta lógica es ¿cómo sé eso? ¿qué argumentos o "cosas" se ponen dentro de una función?

Para ello hay dos enfoques que puedes seguir

1. poner en tu consola `?nombre_de_la_funcion()` y notarás que en **Help** aparecerá automáticamente información de este. Esta información está contenida en [CRAN](https://cran.r-project.org/)

2. Buscar información del paquete en [https://www.rdocumentation.org/](https://www.rdocumentation.org/)


```r
install.packages("tidyverse") # Instalar
remove.packages("tidyverse") # Remover
```

También funciona la noción de una *cadena* de paquetes concatenados por `c()`


```r
install.packages(c("tidyverse", "sjPlot", "sjmisc")) # Primera vez
```

**2. Llamar paquetes**

Este proceso lo haremos con la función `library()`. Cuando la utilicemos nos dirá algunas informaciones importantes: (1) su versión, (2) los paquetes que trae si es una colección de estos (*attaching packages*) y (3) los **conflictos** con otros paquetes que ya tienes cargados.


```r
library(tidyverse)
```

```
## Warning: package 'tidyverse' was built under R version 4.0.5
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
```

```
## v ggplot2 3.3.3     v purrr   0.3.4
## v tibble  3.1.0     v dplyr   1.0.6
## v tidyr   1.1.3     v stringr 1.4.0
## v readr   1.4.0     v forcats 0.5.1
```

```
## Warning: package 'readr' was built under R version 4.0.5
```

```
## Warning: package 'dplyr' was built under R version 4.0.5
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

Ahora, intentemos ocupar la función `frq()` del paquete `sjmisc()`  para ver la distribución de equipo dentro de la base *datos*

```r
frq(datos$equipo)
```

Sí, no es un error del sitio web. Aparecerá el siguiente error: 


```r
# > Error in frq(datos$equipo) : could not find function "frq"
```

Tal como dice, no se puede encontrar la función `frq()`. Por eso, no deben olvidar **llamar** a los paquetes, que como vimos en la sesión, es como "encender" el interruptor


```r
library(sjmisc)
```

También existe la opción de indicar desde qué paquete proviene la función, y eso automáticamente los llama pero **solo en el momento en que se ocupa la función (`paquete::funcion()`)


```r
sjmisc::frq(datos$equipo)
```

```
## 
## x <character>
## # total N=3  valid N=3  mean=2.00  sd=1.00
## 
## Value     | N | Raw % | Valid % | Cum. %
## ----------------------------------------
## Dafne     | 1 | 33.33 |   33.33 |  33.33
## Nicolás   | 1 | 33.33 |   33.33 |  66.67
## Valentina | 1 | 33.33 |   33.33 | 100.00
## <NA>      | 0 |  0.00 |    <NA> |   <NA>
```

Esto será muy útil cuando existan paquetes que tengan funciones que están en conflicto.

### Una forma fácil de cargar paquetes: `pacman`

`pacman` es un paquete que *literalmente* se comió procesos de R `base` y las simplificó en funciones únicas y más intuitivas. 

Para comentar utilizándolo hay que hacer instalación tradicional


```r
install.packages("pacman")
library(pacman)
```

Luego, la función que utilizaremos con frecuencia será `p_load()` que es una función que permite cargar librerías e instalarlas en caso de que estas no hayan sido descargadas. Básicamente `p_load()` resume las funciones `library()` e `install.packages()`, y optimiza esta relación entre ambas pues solo las aplica cuando son necesarias (if `requiere()``), es decir, **¡no te reinicia R si ya está instalada la librería!**


```r
pacman::p_load(tidyverse,
               sjPlot,
               sjmisc)
```

Por último, muchas veces ocurrirá que hay paquetes que dependen de otros, pues dada su complejidad, requieren de otras funcionalidades que ya han sido desarrolladas en otras extensiones. Esto, las **dependencias** no será un tema del cuál preocuparte pues `pacman::p_load()` lo hace solito. 

# Reporte de progreso
  
¡Recuerda rellenar tu [reporte de progreso](https://learn-r.formr.org/). En tu correo electrónico está disponible el código mediante al cuál debes acceder para actualizar tu estado de avance del curso.


# Recomendaciones

El contenido de estos primers es libre y gratis, y provienen del libro de Hadley Wickham y Garrett Grolemund [*R for Data Science*](https://r4ds.had.co.nz/).

  - [RProjects](https://r4ds.had.co.nz/workflow-projects.html#rstudio-projects)
	- [Programming Basics](https://rstudio.cloud/learn/primers/1.2)
	- [Working with Tibbles](https://rstudio.cloud/learn/primers/2.1)
