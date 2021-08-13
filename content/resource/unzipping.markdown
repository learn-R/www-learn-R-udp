---
title: "Descomprimir archivos"
date: "2021-08-13"
menu:
  resource:
    parent: Otros materiales
type: docs
weight: 4
toc: true
---

Debido a que los Rproject  de RStudio normalmente consisten en múltiples archivos (scripts de R, conjuntos de datos, salida gráfica, etc.) la forma más fácil de distribuirlos para los ejemplos, tareas y proyectos es combinar todos los diferentes archivos en una sola colección comprimida llamada **archivo comprimido**.

Cuando descomprimes un archivo comprimido, tu sistema operativo extrae todos los archivos que contiene en una nueva carpeta de tu ordenador.

Descomprimir archivos en macOS es trivial, pero descomprimir archivos en Windows puede ser molesto, si no sigues los siguientes pasos. Aquí tienes una guía útil para descomprimir archivos tanto en macOS como en Windows.


## Descomprimir archivos en macOS

Haz doble clic en el archivo `.zip` descargado. macOS creará automáticamente una nueva carpeta con el mismo nombre que el archivo `.zip`, y todo el contenido del archivo estará dentro. Haz doble clic en el archivo de proyecto de RStudio (`.Rproj`) para empezar.


```r
knitr::include_graphics("/img/unzipping/unzip-mac.png", error = FALSE)
```

![](/img/unzipping/unzip-mac.png)<!-- -->


## Descomprimir archivos en Windows

***tl;dr**: Haz clic con el botón derecho del ratón en el archivo `.zip`, selecciona "Extraer todo...", y trabaja con la carpeta descomprimida resultante.*

A diferencia de macOS, Windows no descomprime automáticamente las cosas por ti. Si haces doble clic en el archivo `.zip`, Windows te mostrará lo que hay dentro, pero lo hará sin extraer nada. ¡Esto ~~puede ser~~ increíblemente confuso! Esto es lo que parece: las únicas pistas de que esta carpeta es realmente un archivo `.zip` son que hay una pestaña de "Herramientas de carpetas comprimidas" en la parte superior, y hay una columna de "Ratio" que muestra cuánto está comprimido cada archivo. 

<img src="/img/unzipping/inside-zip-windows.png" width="80%" />

Es muy tentador intentar abrir archivos desde esta vista. Sin embargo, si lo hace, las cosas se romperán y no podrá trabajar correctamente con ninguno de los archivos de la carpeta comprimida. Si abre el archivo del Proyecto R, por ejemplo, RStudio apuntará a un extraño directorio de trabajo enterrado en alguna carpeta temporal:

<img src="/img/unzipping/temp-wd-windows.png" width="60%" />

Lo más probable es que no puedas abrir ningún archivo de datos ni guardar nada, lo cual será frustrante.

En lugar de eso, tienes que hacer clic con el botón derecho del ratón en el archivo `.zip` y seleccionar "Extraer todo...":

<img src="/img/unzipping/extract-windows-1.png" width="60%" />

A continuación, elige dónde quieres descomprimir todos los archivos y haz clic en "Extraer"

<img src="/img/unzipping/extract-windows-2.png" width="60%" />

Finalmente deberías tener una carpeta real con todo el contenido del archivo comprimido. Abre el archivo del proyecto R y RStudio apuntará al directorio de trabajo correcto y todo funcionará.

<img src="/img/unzipping/extract-windows-3.png" width="60%" />
