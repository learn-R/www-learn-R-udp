---
title: "Regresiones log�sticas"
linktitle: "6: Regresiones logisticas"
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



## 0. Objetivos del pr�ctico

Este pr�ctico tiene por objetivo presentar c�mo crear modelos de regresi�n log�stica en R, con predictores categ�ricos y cuantitativos; c�mo exponenciar los coeficientes para facilitar su interpretaci�n; y, por �ltimo, herramientas de visualizaci�n de los modelos generados. 

## 1. Recursos del pr�ctico

Este pr�ctico fue elaborado con datos de la [**Encuesta Suplementaria de Ingresos (ESI)**](https://www.ine.cl/estadisticas/sociales/ingresos-y-gastos/encuesta-suplementaria-de-ingresos) en su versi�n 2020. Cuando trabajen con alg�n set de datos, nunca olviden revisar la documentaci�n metodol�gica anexa, as� como el [libro de c�digos](https://www.ine.cl/docs/default-source/encuesta-suplementaria-de-ingresos/bbdd/manual-y-gu�a-de-variables/2020/personas-esi-2020.pdf?sfvrsn=f196cb4e_4) correspondiente. 

- [<i class="fas fa-file-archive"></i> `06-bloque.zip`](https://github.com/learn-R/09-class/raw/main/06-bloque.zip)


## 2. Librer�as a utilizar

En este pr�ctico utilizaremos seis paquetes

1. `pacman`: este facilita y agiliza la lectura de los paquetes a utilizar en R

2. `sjmisc`: explorar datos

3. `tidyverse`: colecci�n de paquetes, del cu�l utilizaremos `dplyr` y `haven`

4. `haven`: para transformar variables

5. `dplyr`: nos permite seleccionar variables de un set de datos

6. `sjPlot`: para construir tablas y gr�ficos

# Pasos del procesamiento

## 1. Cargar librer�as

Como en las pr�cticas anteriores, empleamos la funci�n `p_load` de la librer�a `pacman` 


```r
pacman::p_load(sjmisc,
               tidyverse,
               haven,
               dplyr,
               sjPlot)
```

## 2. Cargar datos

Como se se�al� anteriormente, en este pr�ctico se trabajar� con los datos de la **Encuesta Suplementaria de Ingresos (ESI)** en su versi�n 2020. Esta se encuentra en la carpeta "input/data", en formato .rds, habiendo sido procesada anteriormente. Por ello, empleamos la funci�n `readRDS()` de la librer�a `base` de R.




```r
datos <- readRDS("../input/data/datos_proc.rds")
```

Podemos darnos cuenta de que el set de datos presenta 71.935 observaciones (o filas), y 4 variables (o columnas), que incluyen a las variables `ingresos`, `educacion`, `sexo` y `edad`.

## 3. Explorar datos

A continuaci�n, usaremos la funci�n `view_df()` del paquete `sjPlot`, que presenta un resumen de las variables contenidas en el set de datos, que nos permitir� identificar la _etiqueta_ de cada variable y de cada una de sus alternativas de respuesta. 


```r
sjPlot::view_df(datos,
                encoding = 'UTF-8')
```

```
## d3_4_porcentaje [225], d3_5_porcentaje [229], d3_6_porcentaje [233], d3_7_porcentaje [238], d14_2_opcion2 [299], d14_5_opcion2 [309], d14_6_opcion2 [312]
```

```
## Warning in sprintf("%i", as.integer(range(x[[index]], na.rm = T))): NAs
## introduced by coercion to integer range
```

<table style="border-collapse:collapse; border:none;">
<caption>Data frame: datos</caption>
<tr>
<th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">ID</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Name</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Label</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Values</th><th style="border-bottom:double; font-style:italic; font-weight:normal; padding:0.2cm; text-align:left; vertical-align:top;">Value Labels</th>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ano_trimestre</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Año del trimestre</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 2020-2020</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">mes_central</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Mes central</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Diciembre – Febrero<br>Enero – Marzo<br>Febrero – Abril<br>Marzo – Mayo<br>Abril – Junio<br>Mayo – Julio<br>Junio – Agosto<br>Julio – Septiembre<br>Agosto – Octubre<br>Septiembre – Noviembre<br>Octubre – Diciembre<br>Noviembre – Enero</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">region</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Región</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Tarapacá<br>Antofagasta<br>Atacama<br>Coquimbo<br>Valparaíso<br>O'Higgins<br>Maule<br>Biobío<br>La Araucanía<br>Los Lagos<br>Aysén<br>Magallanes<br>Metropolitana<br>Los Ríos<br>Arica y Parinacota<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">r_p_c</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Comuna (código único territorial)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1101<br>1107<br>1401<br>1402<br>1403<br>1404<br>1405<br>2101<br>2102<br>2103<br>2104<br>2201<br>2202<br>2203<br>2301<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Iquique<br>Alto Hospicio<br>Pozo Almonte<br>Camiña<br>Colchane<br>Huara<br>Pica<br>Antofagasta<br>Mejillones<br>Sierra Gorda<br>Taltal<br>Calama<br>Ollagüe<br>San Pedro de Atacama<br>Tocopilla<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">estrato</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Estrato muestral consolidado mbackground-color:#eeeeeeo nuevo/mbackground-color:#eeeeeeo<br>antiguo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1021-16300120</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">tipo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Tipo de estrato</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ciudad<br>Resto de Área Urbana (RAU)<br>Rural</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">conglomerado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Identificador de conglomerado consolidado mbackground-color:#eeeeeeo<br>nuevo/mbackground-color:#eeeeeeo antiguo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">&lt;output omitted&gt;</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">8</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">id_identificacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Identificador único del hogar encuestado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 44539-NA</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">idrph</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Identificador único de personas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 20256-1453197462</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">10</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ano_encuesta</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Año de encuestaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 2020-2020</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">11</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">mes_encuesta</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Mes de encuestaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 10-12</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Número del hogar dentro de la vivienda</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">13</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">nro_linea</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Número de línea de la persona</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-14</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">14</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">edad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Edad de la persona</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0-106</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">15</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sexo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Hombre<br>Mujer</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">16</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">parentesco</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Parentesco con jefe de hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Jefe de hogar<br>Conyuge<br>Conviviente<br>Hijo/a o hijastro/a<br>Yerno o nuera<br>Nieto/a<br>Hermano/a) o cuñado/a<br>Padres o suegros<br>Otro pariente<br>No pariente<br>Servicio doméstico<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">17</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">curso</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Curso más alto aprobado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">9<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Curso desconocido<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">18</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">nivel</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Nivel educacional más alto aprobado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>14<br>88<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Nunca estudió<br>Sala cuna<br>Kínder<br>Básica o primaria<br>Media común<br>Media técnico profesional<br>Humanidades<br>Centro de formación técnica (CFT)<br>Instituto profesional (IP)<br>Universitario<br>Postítulo<br>Magíster<br>Doctorado<br>Normalista<br>No sabe<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">19</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">termino_nivel</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Término de nivel educacional</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">20</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">est_conyugal</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Estado conyugal</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1<br>2<br>3<br>4<br>5<br>6<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No aplica<br>Casado/a<br>Conviviente<br>Soltero/a<br>Viudo/a<br>Separado/a de hecho o anulado/a<br>Divorciado/a<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">21</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">proveedor</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Identificación de proveedor económico principal<br>del hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No corresponde<br>Proveedor principal<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">22</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">nacionalidad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Nacionalidad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">4<br>8<br>12<br>16<br>20<br>24<br>28<br>31<br>32<br>36<br>40<br>44<br>48<br>50<br>51<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Afganistán<br>Albania<br>Argelia<br>Samoa Oriental<br>Andorra<br>Angola<br>Antigua y Barbuda<br>Azerbaiyán<br>Argentina<br>Australia<br>Austria<br>Bahamas<br>Bahréin<br>Bangladés<br>Armenia<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">23</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a1. La semana pasada, es decir, entre lunes y<br>domingo, ¿trabajó por lo menos una</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">24</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a2. Independientemente de lo que acaba de decir,<br>¿hizo algún negocio, 'pololo' u</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">25</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a3. Por ese trabajo, ¿recibió o recibirá un pago,<br>ya sea en dinero o en especie?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">26</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a4. Ese trabajo, ¿fue realizado para la empresa o<br>negocio perteneciente a un mie</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">27</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a5. Aunque no trabajó la semana pasada, ¿tenía<br>durante dicho periodo un empleo,</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">28</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a6. ¿Por qué razón no trabajó la semana pasada?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>88<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Vacaciones o permisos<br>Licencia médica<br>Por horario o jornada variable o flexible<br>Huelga o conflicto laboral<br>Asistencia a cursos de capacitación<br>Problemas de salud<br>Suspensión temporal del trabajo<br>No tuvo pedidos o clientes<br>Razones climáticas o catástrofes naturales<br>Su trabajo es ocasional<br>Razones económicas o técnicas de la empresa o negocio<br>Su trabajo es estacional<br>Clausura de negocio o de empresa<br>Otras razones (especificar)<br>No sabe<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">29</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a6_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a6_otro. Especifica a6 = Otras razones de ausencia<br>del trabajo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">30</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a6_otro_covid</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Glosa de a6_otro, ¿Se asocia a pandemia COVID-19?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">31</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a6_orig</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-14</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">32</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a7. Durante este periodo en que no trabajó,<br>¿siguió recibiendo sueldo o ganancia</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">33</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a8</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">a8. ¿En cuánto tiempo más volverá a trabajar?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">En cuatro semanas o menos<br>En más de cuatro semanas<br>No sabe cuando volverá a trabajar</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">34</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">a9. Si las restricciones por la pandemia del<br>COVID-19 se terminaran en las próxi</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">35</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b1. Grupo ocupacional según CIUO 08 - 1 dígito</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Directores, gerentes y administradores<br>Profesionales, científicos e intelectuales<br>Técnicos y profesionales de nivel medio<br>Personal de apoyo administrativo<br>Trabajadores de los servicios y vendedores de comercios y mercados<br>Agricultores y trabajadores calificados agropecuarios, forestales y pesqueros<br>Artesanos y operarios de oficios<br>Operadores de instalaciones, maquinas y ensambladores<br>Ocupaciones elementales<br>Otros no identificados<br>Sin clasificación</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">36</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b2. ¿Realizó ese trabajo…</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">…para su propio negocio, empresa o actividad por cuenta propia?<br>…como empleado u obrero para un patrón, empresa, negocio o institución, o empleada de casa particular?<br>…para el negocio, empresa o actividad por cuenta propia de un miembro de esta familia?</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">37</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b3. ¿Por ese trabajo...</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">…recibe sueldo o salario?<br>…retira dinero?<br>…retira sólo mercadería?<br>…no retira dinero ni mercadería?</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">38</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b4. En ese trabajo, ¿emplea personas para su<br>negocio o actividad? No incluya a m</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">39</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b5. El negocio, empresa o institución donde<br>trabajó la semana pasada era…</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">…estatal?<br>…privada?<br>…hogar particular?</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">40</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b6. ¿Su trabajo en ese hogar es...</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">…servicio doméstico puertas afuera?<br>…servicio doméstico puertas adentro?<br>…otros servicios?</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">41</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7a_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7a_1. Su empleador, ¿cotiza por usted en el<br>sistema previsional o de pensión?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">42</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b7a_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b7a_2. Su empleador, ¿cotiza por usted en el<br>sistema de salud (público o privado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">43</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7a_3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7a_3. Su empleador, ¿cotiza por usted en el<br>sistema de seguro de desempleo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">44</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b7b_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b7b_1. En este trabajo, ¿tiene derecho, aunque no<br>utilice, a vacaciones anuales?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">45</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7b_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7b_2. En este trabajo, ¿tiene derecho, aunque no<br>utilice, a días pagados por en</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">46</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b7b_3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b7b_3. En este trabajo, ¿tiene derecho, aunque no<br>utilice, a permiso por materni</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">47</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7b_4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b7b_4. En este trabajo, ¿tiene derecho, aunque no<br>utilice, a servicio de guarder</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">48</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b8</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b8. En ese empleo, ¿tiene contrato escrito?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">49</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b9. ¿La duración de ese contrato o acuerdo de<br>trabajo es...</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">…definido, es decir, con plazo de término o a plazo fijo?<br>…indefinido, es decir, sin plazo de término?<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">50</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b10</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b10. ¿Su contrato o acuerdo definido es...</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">…limitado a la temporada?<br>…limitado al término de la obra, proyecto o actividad?<br>…limitado a menos de tres meses por contrato o acuerdo (a prueba, práctica)?<br>…renovable una vez al año?<br>…por reemplazo?<br>…no sabe?<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">51</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b11_proxy</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b11_proxy. b11. ¿El pago por su actividad<br>principal fue a través de sueldo, sala</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">52</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b12. ¿Está contratado o tiene un acuerdo de<br>trabajo...</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">…directamente con la empresa en donde trabaja?<br>…con un contratista o subcontratista de bienes o servicios?<br>…con una empresa de servicios temporales o suministradora de trabajadores?<br>…con un enganchador (contratista agrícola)?<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">53</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b13_rev4cl_caenes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b13. Rama de actividad económica de empresa que le<br>paga según según CAENES (adap</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Agricultura, ganadería, silvicultura y pesca<br>Explotación de minas y canteras<br>Industrias manufactureras<br>Suministro de electricidad, gas, vapor y aire acondicionado<br>Suministro de agua<br>Construcción<br>Comercio al por mayor y al por menor<br>Transporte y almacenamiento<br>Actividades de alojamiento y de servicio de comidas<br>Información y comunicaciones<br>Actividades financieras y de seguros<br>Actividades inmobiliarias<br>Actividades profesionales, científicas y técnicas<br>Actividades de servicios administrativos y de apoyo<br>Administración pública y defensa<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">54</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i1. La empresa, negocio o institución que le paga<br>su sueldo, ¿está registrada en</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Si<br>No sabe, pero la empresa entrega boleta o factura por las ventas o prestación de servicios<br>No, ningún tipo de registro<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">55</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i2. La empresa, negocio o institución que le paga<br>su sueldo, ¿cuenta con los ser</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Si<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">56</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i3. ¿Cuál es el nombre de la empresa, negocio o<br>institución que le paga su sueld</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">(Informante identifica nombre)<br>No tiene nombre<br>Es un hogar particular<br>Trabaja para otro asalariado<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">57</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i3_v</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i3_v. Variable creada a partir de la verificación<br>de glosa con nombre de la empr</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0-1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">58</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i4. La empresa, negocio o actividad en la que<br>trabaja, ¿está registrada en el Se</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Si<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">59</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i5. La empresa, negocio o actividad en la que<br>trabaja ¿se encuentra registrada c</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">...trabajador independiente (con boleta de honorarios)?<br>...persona natural (el RUT de la empresa es igual al RUT del dueño)<br>...otro tipo de registro (Limitada, E.I.R.L, S.A, Spa)?<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">60</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i6. ¿La empresa, negocio o actividad por cuenta<br>propia...</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">...acude a los servicios de un contador para llevar la contabilidad completa?<br>...se encuentra acogido al régimen de contabilidad simplificada?<br>...sólo cuenta con registros personales de gastos e ingresos?<br>No cuenta con ningún tipo de contabilidad<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">61</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i7. A través de la contabilidad, ¿se pueden<br>separar los gastos del negocio de lo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Si<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">62</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b14_rev4cl_caenes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b14. Rama de actividad económica de empresa donde<br>trabaja según según CAENES (ad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Agricultura, ganadería, silvicultura y pesca<br>Explotación de minas y canteras<br>Industrias manufactureras<br>Suministro de electricidad, gas, vapor y aire acondicionado<br>Suministro de agua<br>Construcción<br>Comercio al por mayor y al por menor<br>Transporte y almacenamiento<br>Actividades de alojamiento y de servicio de comidas<br>Información y comunicaciones<br>Actividades financieras y de seguros<br>Actividades inmobiliarias<br>Actividades profesionales, científicas y técnicas<br>Actividades de servicios administrativos y de apoyo<br>Administración pública y defensa<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">63</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b15_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b15_1. En todo el país, ¿cuántas personas trabajan<br>en esa empresa, negocio, inst</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Menos de 5<br>De 5 a 10 personas<br>Entre 11 y 49<br>Entre 50 y 199<br>200 y más personas<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">64</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b15_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b15_2. Número exacto, hasta 10 personas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Número desconocido</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">65</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b16</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b16. En la semana que terminó el domingo pasado,<br>¿dónde realizo principalmente s</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">En instalaciones u oficina del cliente o empleador<br>En la casa del empleador o cliente<br>En instalaciones u oficinas propias o arrendadas<br>En la oficina, local, taller o fábrica, anexo a su hogar (en el mismo predio)<br>En su propio hogar<br>En la calle o vía pública<br>En obras de construcción, mineras o similares<br>En un predio agrícola o espacio marítimo o aéreo<br>En otros lugares (especifique)<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">66</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b16_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b16_otro. Especifica b16 = En otros lugares</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">67</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b16_otro_covid</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Glosa de b16_otro, ¿Se asocia a pandemia COVID-19?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">68</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b16_orig</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-99</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">69</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b17_mes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b17_mes. ¿Y desde cuando trabaja para ese<br>empleador, en ese negocio o actividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Enero<br>Febrero<br>Marzo<br>Abril<br>Mayo<br>Junio<br>Julio<br>Agosto<br>Septiembre<br>Octubre<br>Noviembre<br>Diciembre<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">70</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b17_ano</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b17_año. ¿Y desde cuando trabaja para ese<br>empleador, en ese negocio o actividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1950-9999</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">71</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b18_region</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b18_region. ¿En qué comuna se ubica la empresa,<br>negocio, institución o actividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Tarapacá<br>Antofagasta<br>Atacama<br>Coquimbo<br>Valparaíso<br>O'Higgins<br>Maule<br>Biobío<br>La Araucanía<br>Los Lagos<br>Aysén<br>Magallanes<br>Metropolitana<br>Los Ríos<br>Arica y Parinacota<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">72</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b18_varias</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b18_varias. ¿En qué comuna se ubica la empresa,<br>negocio, institución o actividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">73</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b18_codigo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">b18_codigo. ¿En qué comuna se ubica la empresa,<br>negocio, institución o actividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">4<br>8<br>12<br>16<br>20<br>24<br>28<br>31<br>32<br>36<br>40<br>44<br>48<br>50<br>51<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Afganistán<br>Albania<br>Argelia<br>Samoa Oriental<br>Andorra<br>Angola<br>Antigua y Barbuda<br>Azerbaiyán<br>Argentina<br>Australia<br>Austria<br>Bahamas<br>Bahréin<br>Bangladés<br>Armenia<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">74</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b19</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">b19. Durante la semana de referencia, ademas del<br>trabajo descrito, ¿tuvo algún o</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">75</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c2_1_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c2_1_1. Actividad principal: Horas diarias<br>trabajadas habitualmente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">76</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c2_1_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c2_1_2. Actividad principal: Días a la semana<br>trabajados habitualmente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">77</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c2_1_3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c2_1_3. Actividad principal: Total horas semanales<br>trabajadas habitualmente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">78</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c2_2_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c2_2_1. Actividad secundaria: Horas diarias<br>trabajadas habitualmente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">79</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c2_2_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c2_2_2. Actividad secundaria: Días a la semana<br>trabajados habitualmente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">80</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c2_2_3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c2_2_3. Actividad secundaria: Total horas<br>semanales trabajadas habitualmente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">81</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c3_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c3_1. Actividad principal: Horas diarias<br>contratadas o acordadas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">82</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c3_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c3_2. Actividad principal: Días a la semana<br>contratados o acordados</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">83</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c3_3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c3_3. Actividad principal: Total horas semanales<br>contratadas o acordadas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">84</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c4. ¿Le pagan habitualmente las horas extras en su<br>actividad principal?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">85</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c5. La semana pasada, ¿trabajó más horas que las<br>habituales en su actividad prin</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">86</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c6. ¿Cuántas horas más de las habituales trabajó<br>la semana pasada?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">87</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c7. La semana pasada, ¿trabajó menos horas que las<br>habituales en su actividad pr</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c8</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c8. ¿Cuántas horas menos de las habituales trabajó<br>la semana pasada?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">89</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c9. ¿Por qué razón trabajó un número de horas<br>diferente a lo habitual durante la</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Estaba de vacaciones, tenía permiso o había días festivos<br>Razones climáticas o catástrofes naturales<br>Enfermedad, incapacidad temporal o accidente<br>Era temporada baja<br>Hubo menos horas de trabajo por razones técnicas o económicas<br>Razones personales o responsabilidades familiares<br>Tuvo horario variable, flexible o similar [trabajó menos horas de las habituales]<br>Comenzó o cambió de empleo o actividad<br>Conflicto laboral<br>Participó en cursos o seminarios propios de su trabajo<br>Tuvo actividades de representación sindical<br>Por término de empleo o actividad sin haber comenzado otro<br>Había que terminar un proyecto, tarea, obra o faena<br>Tuvo horario variable, flexible o similar [trabajó más horas de las habituales]<br>Era temporada alta<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">90</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c9_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c9_otro. Especifica c9 = Otras razones de un<br>número de horas diferente a lo habi</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">91</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c9_otro_covid</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Glosa de c9_otro, ¿Se asocia a pandemia COVID-19?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">92</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c9_orig</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-99</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">93</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c10</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c10. Si de usted dependiera, ¿trabajaría<br>habitualmente más horas de las que trab</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">94</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c11</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">c11. Si se diera la posibilidad, ¿estaría<br>disponible para trabajar más horas a l</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí, de inmediato<br>En los próximos quince días<br>En un mes más<br>No tiene disponibilidad<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">95</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">c12. ¿Cuál es la razón por la que no trabaja más<br>horas?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">La empresa no dispone de más horas de trabajo / No hay más trabajo<br>No hay más clientes, temporada baja<br>No cancelan las horas extras<br>Razones personales<br>Cuidado de personas dependientes<br>No hay capital, falta local, no hay mercadería<br>Razones de estudio<br>Factores climáticos<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">96</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e2. En las cuatro últimas semanas, hasta el<br>domingo de la semana de referencia,</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">97</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_1. Gestión de búsqueda de empleo: Envió<br>currículum a empresas o instituciones</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">98</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_2. Gestión de búsqueda de empleo: Consultó<br>directamente con empleadores</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_3. Gestión de búsqueda de empleo: Pidió a<br>conocidos o familiares que le recom</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">100</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_4. Gestión de búsqueda de empleo: Revisó y<br>contestó anuncios (diarios, intern</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">101</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_5. Gestión de búsqueda de empleo: Se inscribió<br>o revisó los anuncios en la Of</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">102</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_6. Gestión de búsqueda de empleo: Realizó<br>gestiones para establecerse por su</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">103</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_7. Gestión de búsqueda de empleo: Estuvo<br>buscando clientes o pedidos</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">104</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_8</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_8. Gestión de búsqueda de empleo: Puso anuncios<br>(diarios, internet, radios, r</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">105</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_9. Gestión de búsqueda de empleo: Participó en<br>una prueba o entrevista para c</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">106</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_10</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_10. Gestión de búsqueda de empleo: Consultó con<br>agencias de empleo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">107</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_11</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_11. Gestión de búsqueda de empleo: Actualizó su<br>currículum publicado en Inter</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">108</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e3_12. Gestión de búsqueda de empleo: Nada</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">109</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_total</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e3_total. Número de gestiones realizadas para<br>buscar empleo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">110</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e4. ¿Cuál es el motivo principal por el cual está<br>buscando otro trabajo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Desea un trabajo con mayores ingresos<br>Para mejorar su calidad de vida<br>Para mejorar sus condiciones de trabajo<br>Desea un empleo más acorde a su formación<br>Siente inseguridad en su empleo actual<br>Considera su actividad actual como provisional<br>Dejó de buscar<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">111</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e5. Hasta el domingo de la semana pasada, ¿cuándo<br>fue la última vez que buscó tr</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Cuatro semanas o menos<br>Sólo la semana de entrevista<br>Más de cuatro semanas y menos de dos meses<br>Más de dos meses</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">112</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e5_dia</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e5_dia. Especifique la fecha de su última<br>búsqueda: Día</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">113</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e5_sem</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e5_sem. Especifique la fecha de su última<br>búsqueda: Semana</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">114</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e5_mes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e5_mes. Especifique la fecha de su última<br>búsqueda: Mes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">115</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e5_ano</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e5_año. Especifique la fecha de su última<br>búsqueda: Año</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">8888<br>9999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">116</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e6_mes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e6_mes. ¿Desde cuándo ha estado buscando trabajo?<br>Meses</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Enero<br>Febrero<br>Marzo<br>Abril<br>Mayo<br>Junio<br>Julio<br>Agosto<br>Septiembre<br>Octubre<br>Noviembre<br>Diciembre<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">117</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e6_ano</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e6_años. ¿Desde cuándo ha estado buscando trabajo?<br>Años</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">8888<br>9999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">118</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e7. ¿Qué tipo de jornada de trabajo está buscando?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Jornada completa<br>Jornada pbackground-color:#eeeeeeial<br>Cualquiera<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">119</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e9. ¿Por qué razón no buscó un empleo o no ha<br>hecho preparativos para iniciar o</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Iniciará pronto una actividad por cuenta propia<br>Encontró un empleo que empezará pronto<br>Por responsabilidades familiares permanentes (cuidado de niños o personas dependientes)<br>Estaba estudiando o preparando estudios<br>Es jubilado/a<br>Es rentista<br>Es pensionado/a o montepiada<br>Por motivos de salud permanentes<br>Espera la estación de mayor actividad<br>Por motivos de salud temporales<br>Por responsabilidades familiares de carácter temporal<br>Estaba embarazada<br>Espera los resultados de un proceso de selección o que lo llamen<br>Algún miembro del hogar no se lo permitió<br>Cree que por su edad no le darán empleo<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">120</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e9_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">121</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e9_otro_covid</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Glosa de e9_otro, ¿Se asocia a pandemia COVID-19?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">122</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e9_orig</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-22</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">123</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e10</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e10. Durante las últimas cuatro semanas: a) ¿Hizo<br>gestiones para iniciar un nego</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">124</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e11</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e11. Si durante la semana pasada, hubiera<br>encontrado un trabajo, ¿estaría dispon</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">125</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e12. ¿Por qué motivo no estaría disponible para<br>trabajar?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Iniciará pronto una actividad por cuenta propia<br>Encontró un empleo que empezará pronto<br>Estaba estudiando o empezará a estudiar pronto<br>Por responsabilidades familiares permanentes (cuidado de niños o personas dependientes)<br>Es pensionado/a o montepiada<br>Es jubilado/a<br>Es rentista<br>Por motivos de salud permanentes<br>Por motivos de salud temporales<br>Por embarazo<br>Por responsabilidades familiares de carácter temporal<br>No quiere o no necesita trabajar<br>Otra razón</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">126</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e12_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e12_otro. Especifica e12 = Otras razones de no<br>disponibilidad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">127</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e12_otro_covid</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Glosa de e12_otro, ¿Se asocia a pandemia COVID-19?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">128</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e12_orig</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-13</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">129</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e13</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e13. ¿Ha tenido anteriormente algún empleo o<br>alguna actividad por cuenta propia</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">130</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i11</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i11. Comparando los ingresos recibidos por su<br>actividad principal en el mes de _</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>77<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Disminuyeron<br>Se mantuvieron<br>Aumentaron<br>No aplica<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">131</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i12. Comparando con sus ingresos habituales,<br>¿cuánto de los ingresos de su activ</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Más de la mitad<br>La mitad<br>Menos de la mitad<br>No recibió ingresos<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">132</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_1. Medidas para financiar los gastos del<br>hogar: Reducir gastos del hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">133</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_2. Medidas para financiar los gastos del<br>hogar: Trabajar más horas en su act</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">134</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_3. Medidas para financiar los gastos del<br>hogar: Búsqueda de otro trabajo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">135</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_4. Medidas para financiar los gastos del<br>hogar: Usar ahorros</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">136</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_5. Medidas para financiar los gastos del<br>hogar: Esperar ayuda de un familiar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">137</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_6. Medidas para financiar los gastos del<br>hogar: Esperar ayuda del Estado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">138</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_7. Medidas para financiar los gastos del<br>hogar: Esperar ayuda de institucion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">139</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_8</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_8. Medidas para financiar los gastos del<br>hogar: Utilizar tarjeta o línea de</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">140</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_9. Medidas para financiar los gastos del<br>hogar: Solicitar un préstamo bancar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">141</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_10</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_10. Medidas para financiar los gastos del<br>hogar: Solicitar un préstamo banca</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">142</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_11</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_11. Medidas para financiar los gastos del<br>hogar: No hará nada</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">143</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_88</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_88. Medidas para financiar los gastos del<br>hogar: No sabe</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">144</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">i13_99. Medidas para financiar los gastos del<br>hogar: No responde</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No<br>Sí</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">145</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_total</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">i13_total. Número de medidas adoptadas para<br>financiar los gastos del hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">146</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e21_mes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e21_mes. Y, hasta cuándo trabajó en esa ocupación<br>o actividad: Mes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">147</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e21_ano</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e21_mes. Y, hasta cuándo trabajó en esa ocupación<br>o actividad: Año</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">8888<br>9999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">148</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e21_tramo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e21_tramo. Tramos desde hasta cuando trabajó en<br>esa ocupación o actividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Entre marzo 2020 a la fecha<br>Entre octubre 2019 a febrero 2020<br>Entre 2017 a septiembre 2019<br>Antes de 2017<br>No sabe / No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">149</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e22</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">¿Por qué razón ya no tiene ese empleo, negocio o<br>actividad por cuenta propia?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Despido<br>Renuncia<br>Fin del contrato, proyecto, faena, temporada o reemplazo<br>Jubilación<br>Término del ejercicio de la actividad por cuenta propia<br>Quiebra o cierre de negocio por razones económicas<br>Quiebra o cierre de negocio por un fenómeno natural o siniestro<br>Quiebra o cierre de negocio por la pandemia del COVID-19<br>Imposibilidad de realizar actividad por cuenta propia por la pandemia del COVID-19<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">150</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e23</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">¿Cuál fue el motivo de su despido?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Por razones de edad (muy joven o edad avanzada)<br>Conflicto con su jefe o superior<br>Falta de calificación o capacitación<br>Ya no hubo más trabajo<br>Discriminación por su aspecto físico<br>Incumplimiento de funciones<br>Enfermedad o incapacidad propia<br>Embarazo y/o incompatibilidad con cuidado de personas en el hogar<br>Reducción de personal<br>Pandemia del COVID-19<br>Otra razón<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">151</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e23_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e23_otro. Especifica e23 = Otra razón de despido</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">152</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">e24</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">¿Cuál fue el motivo de su renuncia?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Realizar estudios o recibir formación<br>Cuidado de niños o de adultos enfermos o incapacitados<br>Motivos de salud<br>Deseaba un trabajo con mayores ingresos<br>Para mejorar su calidad de vida<br>Acoso o falta de respeto a su persona<br>Embarazo<br>Se cansó de ese trabajo<br>Pandemia del COVID-19<br>Otra razón<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">153</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">e24_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">¿Cuál fue el motivo de su renuncia?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">154</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">cae_general</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Código sumario de empleo general</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Menor de 15 años<br>Ocupado tradicional<br>Ocupado no tradicional<br>Ocupado ausente<br>Cesante<br>Busca trabajo por primera vez<br>Iniciador<br>Inactivos que buscaron<br>Inactivos que estuvieron disponibles<br>Inactivos que no buscaron ni estuvieron disponibles</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">155</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">cae_especifico</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Código sumario empleo específico</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Menor de quince años<br>Ocupado tradicional<br>Ocupado sin remuneración tradicional<br>Ocupado no tradicional<br>Ocupado sin remuneración no tradicional<br>Ocupado ausentes con vínculo efectivo<br>Ocupado ausente con pronto retorno<br>Ocupado ausente con sueldo o ganancia<br>Cesante<br>Busca trabajo por primera vez<br>Iniciador (Disponible)<br>Iniciador (No disponible)<br>Razones familiares permanentes (Potencial)<br>Razones familiares permanentes (Habitual)<br>Razones de estudio (Potencial)<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">156</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">categoria_ocupacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Categoría en la ocupación (según adaptación<br>chilena de Clasificación Internacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1<br>2<br>3<br>4<br>5<br>6<br>7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No corresponde<br>Empleador<br>Cuenta propia<br>Asalariado sector privado<br>Asalariado sector público<br>Personal de servicio doméstico puertas afuera<br>Personal de servicio doméstico puertas adentro<br>Familiar o personal no remunerado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">157</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">habituales</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Horas habituales de trabajo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">158</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">efectivas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Horas efectivas trabajadas en la semana de<br>referencia</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">888<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">159</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">cine</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Clasificación Internacional de Nivel Educacional<br>(CINE)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Nunca estudió<br>Educación preescolar<br>Educación primaria (nivel 1)<br>Educación primaria (nivel 2)<br>Educación secundaria<br>Educación técnica (Educación superior no universitaria)<br>Educación universitaria<br>Postitulos y maestría<br>Doctorado<br>Nivel ignorado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">160</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">r_p_rev4cl_caenes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Rama de actividad económica de la empresa o<br>institución que le paga el sueldo o</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Agricultura, ganadería, silvicultura y pesca<br>Explotación de minas y canteras<br>Industrias manufactureras<br>Suministro de electricidad, gas, vapor y aire acondicionado<br>Suministro de agua<br>Construcción<br>Comercio al por mayor y al por menor<br>Transporte y almacenamiento<br>Actividades de alojamiento y de servicio de comidas<br>Información y comunicaciones<br>Actividades financieras y de seguros<br>Actividades inmobiliarias<br>Actividades profesionales, científicas y técnicas<br>Actividades de servicios administrativos y de apoyo<br>Administración pública y defensa<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">161</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">sector</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ocupados según sector</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sector formal<br>Sector informal<br>Hogares como empleadores<br>Sin clasificación</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">162</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ocup_form</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ocupados según formalidad de la ocupación</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ocupado formal<br>Ocupado informal<br>Sin clasificación</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">163</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">tramo_edad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Tramo de edad (en quinquenios)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">15 a 19 años<br>20 a 24 años<br>25 a 29 años<br>30 a 34 años<br>35 a 39 años<br>40 a 44 años<br>45 a 49 años<br>50 a 54 años<br>55 a 59 años<br>60 a 64 años<br>65 a 69 años<br>70 años o más</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">164</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">activ</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Condición de actividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ocupados/as<br>Desocupados/as<br>Fuera de la fuerza de trabajo</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">165</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">fact_cal</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Factor de expansión trimestral con nueva<br>calibración, proyecciones de población</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 13.5-5876.9</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">166</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">orig1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">¿Se considera perteneciente a algún pueblo<br>indígena u originario?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">167</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">orig2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">¿A cuál?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>20<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>Rapa Nui<br>Lican Antai o Atacemaños<br>Quechua<br>Colla<br>Diaguita<br>Kawésqar<br>Yagán o Yámana<br>Chango<br>Otro<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">168</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">orig3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">¿A cuál?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">169</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ocup_honorarios</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Por el trabajo realizado, ¿entregó una boleta de<br>honorarios?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>77</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe / No responde<br>No aplica</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">170</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">mig1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-99</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">171</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">mig2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">172</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">mig2_cod</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1101-99999</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">173</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">mig3</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">&lt;output omitted&gt;</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">174</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">mig3_cod</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 32-999</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">175</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">mig4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-99</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">176</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">mig5</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">177</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">mig5_cod</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1101-99999</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">178</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">mig6</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">&lt;output omitted&gt;</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">179</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">mig6_cod</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 32-862</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">180</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">conglomerado_correlativo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Correlativo ordenado del Identificador de<br>conglomerado mbackground-color:#eeeeeeo nuevo/mbackground-color:#eeeeeeo antiguo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-4830</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">181</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d1_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d1_opción. Su empleo actual, ¿es el mismo que<br>tenía el mes anterior?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">182</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d1_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d1_opciónb. ¿Cuál fue el monto en pesos de su<br>SUELDO NETO (SALARIO NETO)?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">183</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d1_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sueldo o salario neto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-9000000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">184</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ss_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por sueldos y salarios netos</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-9000000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">185</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_1_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_1_opción. Bonos por productividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">186</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_1_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de bonos por productividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 9000.0-2013610.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">187</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_1_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Porcentaje de su sueldo en bonos por productividad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-50</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">188</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_1_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_1_opciónb. ¿Cuánto fue el monto en pesos ($) o<br>porcentaje (%) que representa</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">189</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_1_opcionc</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_1_opciónc. ¿Incluyó este ingreso en el sueldo<br>neto declarado anteriormente?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">190</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_2_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_2_opción. Bonos de otro tipo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">191</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_2_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Monto por bonos de otro tipo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 2013.6-5006580.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">192</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_2_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Porcentaje de su sueldo en bonos de otro tipo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 5-100</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">193</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_2_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_2_opciónb. ¿Cuánto fue el monto en pesos ($) o<br>porcentaje (%) que representa</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">194</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_2_opcionc</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_2_opciónc. ¿Incluyó este ingreso en el sueldo<br>neto declarado anteriormente?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">195</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_3_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_3_opción. Comisiones por ventas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">196</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_3_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto por comisiones por ventas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 10068.1-2500000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">197</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_3_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Porcentaje de su sueldo en comisiones por ventas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-80</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">198</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_3_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_3_opciónb. ¿Cuánto fue el monto en pesos ($) o<br>porcentaje (%) que representa</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">199</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_3_opcionc</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_3_opciónc. ¿Incluyó este ingreso en el sueldo<br>neto declarado anteriormente?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">200</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_4_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_4_opción. Pago por horas extraordinarias</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">201</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_4_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Monto por pago por horas extraordinarias</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 3003.9-2517013.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">202</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_4_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Porcentaje de su sueldo en pago por horas<br>extraordinarias</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 7-15</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">203</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_4_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_4_opciónb. ¿Cuánto fue el monto en pesos ($) o<br>porcentaje (%) que representa</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">204</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_4_opcionc</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_4_opciónc. ¿Incluyó este ingreso en el sueldo<br>neto declarado anteriormente?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">205</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_5_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_5_opción. Otros ingresos variables</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">206</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_5_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto por otros ingresos variables</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 5006.6-1006805.3</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">207</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_5_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Porcentaje de su sueldo en otros ingresos<br>variables</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 5-80</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">208</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_5_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d2_5_opciónb. ¿Cuánto fue el monto en pesos ($) o<br>porcentaje (%) que representa</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">209</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_5_opcionc</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d2_5_opciónc. ¿Incluyó este ingreso en el sueldo<br>neto declarado anteriormente?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">210</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">svar_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por sueldos y salarios variables no<br>incluidos en D1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-5006580.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">211</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_1_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_1_opción. En el mes anterior ¿le proporcionaron<br>beneficios de vivienda?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">212</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_1_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de beneficio por vivienda</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 15019.7-800000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">213</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_1_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Porcentaje de su sueldo en beneficio por vivienda</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 30-95</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">214</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_1_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_1_opción2. ¿Cuál fue el monto ($) o porcentaje<br>(%) de su sueldo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">215</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_2_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_2_opción. En el mes anterior ¿le proporcionaron<br>beneficios de alimentación?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">216</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_2_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de beneficio por alimentación</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1510.2-480631.7</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">217</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_2_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Porcentaje de su sueldo en beneficio por<br>alimentación</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 5-60</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">218</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_2_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_2_opción2. ¿Cuál fue el monto ($) o porcentaje<br>(%) de su sueldo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">219</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_3_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_3_opción. En el mes anterior ¿le proporcionaron<br>beneficios de becas de estudi</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">220</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_3_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de beneficio por becas de estudio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 3020.4-1001316.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">221</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_3_porcentaje</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Porcentaje de su sueldo en beneficio por becas de<br>estudio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 30-60</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">222</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_3_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_3_opción2. ¿Cuál fue el monto ($) o porcentaje<br>(%) de su sueldo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">223</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_4_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_4_opción. En el mes anterior ¿le proporcionaron<br>beneficios de transporte y/o</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">224</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_4_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de beneficio por transporte y/o bencina</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1200.0-400000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">226</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_4_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_4_opción2. ¿Cuál fue el monto ($) o porcentaje<br>(%) de su sueldo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">227</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_5_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_5_opción. En el mes anterior ¿le proporcionaron<br>beneficios de propinas?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">228</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_5_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Monto de beneficio por propinas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 9061.2-315000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">230</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_5_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_5_opción2. ¿Cuál fue el monto ($) o porcentaje<br>(%) de su sueldo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">231</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_6_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_6_opción. En el mes anterior ¿le proporcionaron<br>beneficios de sala cuna o jar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">232</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_6_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de beneficio por sala cuna o jardín infantil</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 10000.0-302041.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">234</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_6_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_6_opción2. ¿Cuál fue el monto ($) o porcentaje<br>(%) de su sueldo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">235</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_7_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_7_opción. En el mes anterior ¿le proporcionaron<br>beneficios de otros (especifi</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">236</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_7_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_7. Otra situación (especifique)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">237</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d3_7_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de beneficio por otros (especifique)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 2452.0-402722.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">239</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_7_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d3_7_opción2. ¿Cuál fue el monto ($) o porcentaje<br>(%) de su sueldo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">240</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">reg_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Total ingresos por regalías (beneficios entregados<br>por el empleador)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-1071408.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">241</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_t_d</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Total ingresos sueldos y salarios</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-9312239.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">242</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d4_dias</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d4_dias. Este sueldo neto, ¿a cuántos días<br>trabajados correspondió?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe / No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">243</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d4_horas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d4_horas. En promedio, ¿cuántas horas diarias<br>trabajó durante esos días?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe / No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">244</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d5_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d5_opción. Su actividad actual, ¿es la misma que<br>tenía en el mes anterior?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">245</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d5_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d5_opciónb. Durante dicho mes, ¿cuál fue el monto<br>en pesos del ingreso neto (sue</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Monto<br>Promedio<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">246</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d5_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de ingresos, ganancias o retiros de la<br>empresa, negocio o actividad por cu</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-15102079.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">247</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d5_promedio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Promedio de ingresos, ganancias o retiros de la<br>empresa, negocio o actividad por</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-18045761.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">248</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">gan_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por ganancias del negocio independiente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-18045761.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">249</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d6_1_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d6_1. Salud (ISAPRE o FONASA)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">250</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d6_2_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d6_2. Pensiones (AFP o IPS ex INP)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">251</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d7_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d7_opción. En el mes anterior ¿usó productos,<br>bienes o servicios de su empresa,</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">252</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d7_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto. Estime el monto que hubiese tenido que<br>pagar por los productos o servicio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1502.0-1501974.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">253</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d7_promedio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Promedio. Estime el monto que hubiese tenido que<br>pagar por los productos o servi</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 2004.5-601456.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">254</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">autoconsumo_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por autoconsumo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-1501974.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">255</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d8_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d8_opción. Su empresa, negocio o actividad por<br>cuenta propia, ¿es desarrollada e</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">256</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d8_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Si tuviese que pagar un arriendo mensual por el<br>espacio que utiliza en esta vivi</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 10000.0-4027221.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">257</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ahorroimp_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Gasto imputado por arriendo de local</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-4027221.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">258</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_t_i</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Total ingresos trabajo independiente</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-18045761.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">259</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_t_i_ai</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos del trabajo independiente ajustado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: -4027221.2-18045761.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">260</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_t_p</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos del trabajo principal</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-18045761.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">261</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d9_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d9_opción. Independiente de lo anterior, durante<br>el mes anterior, ¿percibió ingr</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">262</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d9_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Monto de ingresos de otra ocupación, distinta a la<br>actual</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 3003.9-4530623.8</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">263</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d9_promedio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Promedio de ingresos de otra ocupación, distinta a<br>la actual</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 10022.5-2807118.5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">264</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d9_opcionb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d9_opciónb. A pesar de no haber trabajado la<br>semana pasada, durante el mes anter</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">265</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d9_montob</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Monto de ingresos por algún trabajo u ocupación</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 5000.0-5006580.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">266</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d9_promediob</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Promedio de ingresos por algún trabajo u ocupación</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 15036.4-1203050.8</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">267</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_ot</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos otros trabajos</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-5006580.2</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">268</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_t_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Total ingresos del trabajo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-18045761.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">269</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_t_t_ai</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos del trabajo con ingresos independientes<br>ajustados</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: -4027221.2-18045761.6</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">270</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d10_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d10_opción. Durante el mes anterior ¿percibió<br>ingresos por el arriendo de alguna</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">271</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d10_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Monto de ingresos por arriendo de alguna propiedad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 20136.1-8791554.8</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">272</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d10_promedio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Promedio de ingresos por arriendo de alguna<br>propiedad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 13366.9-2505633.5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">273</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_a_p</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por arriendo de alguna propiedad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-8791554.8</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">274</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d11_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d11_opción. Y durante dicho mes ¿percibió ingresos<br>por el arriendo de otro tipo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">275</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d11_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Monto de ingresos por arriendo de otro tipo de<br>bienes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 30039.5-5034026.5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">276</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d11_promedio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Promedio de ingresos por arriendo de otro tipo de<br>bienes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 20050.8-300676.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">277</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_a_ob</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por arriendos de otros bienes</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-5034026.5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">278</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_a_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Total ingresos por arriendos</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-8791554.8</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">279</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d12_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d12_opción. La vivienda que usted habita ¿es...</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">…arrendada?<br>…propia, pero paga dividendo?<br>…propia, totalmente pagada?<br>…cedida gratuitamente?<br>…cedida por el empleador?<br>…otra situación (especifique)?<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">280</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d12_5_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d12_5_opcion. ¿Es necesaria para el desarrollo de<br>su trabajo?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">281</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d12_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Otra situación (especifique)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">282</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d12_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d12_monto. Monto de arriendo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 999.0-1409527.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">283</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d12_estimacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d12_estimación. Estimación de arriendo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 2013.6-4005264.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">284</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">alq_imp</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Alquiler imputado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">999</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">285</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d13_1_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d13_1_opción. ...intereses por cuentas de ahorro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">286</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d13_1_tramo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d13_1_tramo. Intereses por cuentas de ahorro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">$10.000 o menos<br>$10.001 a $15.000<br>$15.001 a $30.000<br>$30.001 a $50.000<br>$50.001 a $75.000<br>$75.001 a $100.000<br>$100.001 a $300.000<br>$300.001 a $600.000<br>$600.001 a $1.000.000<br>$1.000.001 a $1.250.000<br>$1.250.001 a $1.500.000<br>$1.500.001 a $2.000.000<br>$2.000.001 a $2.500.000<br>$2.500.001 a $3.000.000<br>$3.000.001 a $3.500.000<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">287</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_cah</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por cuentas de ahorro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0-10000000</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">288</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d13_2_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d13_2_opción. ...intereses por depósitos a plazo,<br>fondos mutuos, etc.</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">289</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d13_2_tramo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d13_2_tramo. Intereses por depósitos a plazo,<br>fondos mutuos, etc.</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">$10.000 o menos<br>$10.001 a $15.000<br>$15.001 a $30.000<br>$30.001 a $50.000<br>$50.001 a $75.000<br>$75.001 a $100.000<br>$100.001 a $300.000<br>$300.001 a $600.000<br>$600.001 a $1.000.000<br>$1.000.001 a $1.250.000<br>$1.250.001 a $1.500.000<br>$1.500.001 a $2.000.000<br>$2.000.001 a $2.500.000<br>$2.500.001 a $3.000.000<br>$3.000.001 a $3.500.000<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">290</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_int</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos intereses</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0-10000000</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">291</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d13_3_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d13_3_opción. ...dividendos y ganancias por<br>acciones</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">292</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d13_3_tramo</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d13_3_tramo. Dividendos y ganancias por acciones</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br><span style="omit">&lt;...&gt;</span></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">$10.000 o menos<br>$10.001 a $15.000<br>$15.001 a $30.000<br>$30.001 a $50.000<br>$50.001 a $75.000<br>$75.001 a $100.000<br>$100.001 a $300.000<br>$300.001 a $600.000<br>$600.001 a $1.000.000<br>$1.000.001 a $1.250.000<br>$1.250.001 a $1.500.000<br>$1.500.001 a $2.000.000<br>$2.000.001 a $2.500.000<br>$2.500.001 a $3.000.000<br>$3.000.001 a $3.500.000<br><span style="omit">&lt;... truncated&gt;</span></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">293</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_div</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por dividendos</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0-10000000</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">294</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_p_t</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por rentas de la propiedad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-20000000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">295</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_1_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_1_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de jubilac</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">296</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_1_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_1_opción2. ¿Cuál fue el monto ($) que percibió<br>por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">297</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_1_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por jubilación/pensión de vejez</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 14519.1-4205527.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">298</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_2_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_2_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de jubilac</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">300</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_2_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por jubilación/pensión de invalidez</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 14700.0-1812249.5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">301</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_3_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_3_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de montepí</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">302</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_3_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_3_opción2. ¿Cuál fue el monto ($) que percibió<br>por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">303</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_3_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por montepío o viudez</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 19025.0-1500000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">304</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_jub</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por jubilaciones</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-4205527.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">305</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_4_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_4_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de pensión</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">306</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_4_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_4_opción2. ¿Cuál fue el monto ($) que percibió<br>por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">307</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_4_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por pensión de orfandad</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 7000.0-750987.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">308</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_5_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_5_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de pensión</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">310</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_5_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por pensión básica solidaria de invalidez<br>(PBSI)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 50000.0-180236.9</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">311</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_6_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_6_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de pensión</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">313</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_6_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por pensión básica solidaria de vejez<br>(PBSV)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 16500.0-270355.3</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">314</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_7_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_7_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de pensión</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">315</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_7_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_7_opción2. ¿Cuál fue el monto ($) que percibió<br>por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">316</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_7_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por pensión alimenticia</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 10000.0-3523818.5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">317</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_8_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_8_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de otras p</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">318</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_8_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_8_opción2. ¿Cuál fue el monto ($) que percibió<br>por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">319</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_8_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por otras pensiones</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 2000.0-3805000.9</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">320</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_pen</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por pensiones</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-3805000.9</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">321</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_10_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_10_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de subsid</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">322</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_10_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_10_opción2. ¿Cuál fue el monto ($) que<br>percibió por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">323</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_10_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por subsidio único familiar (SUF)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1409.5-153034.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">324</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_12_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_12_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de otros</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">325</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_12_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_12_opción2. ¿Cuál fue el monto ($) que<br>percibió por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">326</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_12_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por otros subsidios del Estado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1201.6-1001316.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">327</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_sube</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por subsidios del Estado</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-1001316.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">328</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_9_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_9_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de seguro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">329</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_9_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_9_opción2. ¿Cuál fue el monto ($) que percibió<br>por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">330</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_9_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por seguro de desempleo o cesantía</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 5034.0-1500000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">331</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_9_especifique</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_9_especifique. Este ingreso que percibió,<br>¿está asociado a Ley de Protección</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">332</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_subc</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por seguros de desempleo o cesantía</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-1500000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">333</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_11_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">D14_11_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de becas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">334</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_11_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">D14_11_opción2. ¿Cuál fue el monto ($) percibido<br>por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">335</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_11_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por becas de estudio</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-5038622.3</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">336</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">becaest</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por becas de estudios</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-5038622.3</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">337</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_13_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_13_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de donaci</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">338</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_13_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_13_opción2. ¿Cuál fue el monto ($) que<br>percibió por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">339</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_13_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por donaciones de hogar a hogar</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 5000.0-3020415.9</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">340</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_14_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_14_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de donaci</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">341</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_14_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_14_opción2. ¿Cuál fue el monto ($) que<br>percibió por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">342</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_14_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ingresos por donaciones desde el exterior</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 10068.1-1401842.5</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">343</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_bdr</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Total ingresos por becas, donaciones y remesas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-5038622.3</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">344</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_dr</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Total ingresos por donaciones y remesas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-3020415.9</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">345</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_15_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_15_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de salari</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">346</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_15_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_15_opción2. ¿Cuál fue el monto ($) que<br>percibió por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">347</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_15_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por salarios del exterior</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 20026.3-704763.7</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">348</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_16_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_16_opción. Durante el mes anterior ¿percibió<br>ingresos provenientes de otras</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">1<br>2<br>88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Sí<br>No<br>No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">349</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_16_opcion2</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_16_opción2. ¿Cuál fue el monto ($) que<br>percibió por este concepto?</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">88<br>99</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No sabe<br>No responde</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">350</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_16_otro</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">d14_16. Otras fuentes del exterior (especifique)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;"></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">351</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">d14_16_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Ingresos por otras fuentes del exterior</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1200.0-2200000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">352</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_otros</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Total de ingresos del exterior</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-2200000.0</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">353</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_aut_cb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Total ingreso autónomo con transferencias en<br>educación (CB)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-24205527.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">354</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_aut_sb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Total ingreso autónomo sin transferencias en<br>educación (SB)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-24205527.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">355</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">ing_mon_cb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Total ingreso monetario con transferencias en<br>educación (CB)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 0.0-24205527.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">356</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ing_mon_sb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Total ingreso monetario sin transferencias en<br>educación (SB)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 0.0-24205527.4</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">357</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">fact_cal_esi</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Factor de expansión ESI con nueva calibración,<br>proyecciones de población</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 2.8-7070.1</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">358</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">ocup_ref</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Binario ocupados de referencia tabulados de<br>personas</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Ocupados con menos de 1 mes en el empleo actual<br>Ocupados con más de 1 mes en el empleo actual</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">359</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">tim</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Tramos de ingresos mínimos netos según ingresos<br>del trabajo principal</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">1<br>2<br>3<br>4<br>5<br>6<br>7</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0 - $258003 (1SM)<br>$258003 - $516006 (2SM)<br>$516006 - $1032012 (4SM)<br>$1032012 - $1548018 (6SM)<br>$1548018 - $2064024 (8SM)<br>$2064024 - $2580030 (10SM)<br>$2580030 y más</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">360</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">decilh_cb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Decil ingresos del hogar con transferencias en<br>educación (CB)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;" colspan="2"><em>range: 1-10</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">361</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">decilh_sb</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Decil ingresos del hogar sin transferencias en<br>educación (SB)</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee" colspan="2"><em>range: 1-10</em></td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">362</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">imp_d1_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Imputación en d1_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No imputado<br>Imputado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">363</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">imp_d4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Imputación en d4</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No imputado<br>Imputado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">364</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">imp_d5_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Imputación en d5_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No imputado<br>Imputado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">365</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">imp_d12_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Imputación en d12_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No imputado<br>Imputado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">366</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">imp_d12_estimacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Imputación en d12_estimacion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No imputado<br>Imputado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">367</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">imp_d14_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">Imputación en d14_opcion</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top; background-color:#eeeeee">No imputado<br>Imputado</td>
</tr>
<tr>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">368</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">imp_d14_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">Imputación en d14_monto</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">0<br>1</td>
<td style="padding:0.2cm; text-align:left; vertical-align:top;">No imputado<br>Imputado</td>
</tr>

</table>

Podemos ver que tenemos dos variables cuantitativas (`ingresos` y `edad`), y dos variables categ�ricas (`sexo` y `educacion`). Estas �ltimas tienen 2 y 10 categor�as, respectivamente. Sin embargo, en el caso de educacion, el valor `999` indica _Nivel ignorado_, es decir, valores nulos. En consideraci�n de ello, ahora toca recodificar las variables y establecer los datatypes adecuados para poder generar el modelo de forma correcta. 

## 4. Transformaci�n de variables

Primero, debemos cerciorarnos de que el nivel de medici�n corresponda con el datatype de cada columna. Para ello, emplearemos el comando `class()` del paquete `base` de R.



Las variables `ingresos` y `edad` presentan un `class()` de tipo `numeric`, por lo cual no es necesario realizar modificaciones. Sin embargo, las variables `educacion` y `sexo` deben ser transformadas en `factor` para trabajar con ellas de manera adecuada. Para ello, empleamos la funci�n `as_factor()` del paquete `haven`. Una de las ventajas de esta funci�n es que nos permite **mantener las etiquetas** de nuestras variables.


```r
class(datos$educacion)
```

```
## Warning: Unknown or uninitialised column: `educacion`.
```

```
## [1] "NULL"
```

```r
class(datos$sexo)
```

```
## [1] "haven_labelled" "vctrs_vctr"     "double"
```

## 5. Recodificaciones

La variable `educacion` presenta la categor�a de respuesta `Nivel ignorado`, que realmente presenta casos perdidos. Para ello, usaremos la funci�n `recode()` de la librer�a `car`, para transformar tales valores en `NA`. Asimismo, se unificar�n las categor�as `Educaci�n primaria (nivel 1)` y `Educaci�n primaria (nivel 2)`; y renombraremos `Educaci�n t�cnica (Educaci�n superior no universitaria)` como `Educaci�n t�cnica` para facilitar el an�lisis. Tambi�n nos damos cuenta de que las niveles (`levels`) de la variable no est�n ordenados de menor a mayor nivel educacional, por lo cual utilizaremos el argumento `levels` de la funci�n `as.factor` del paquete `base` de R, para ordenarlos de forma adecuada. 
















