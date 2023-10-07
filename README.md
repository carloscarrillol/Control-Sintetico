---
title: "Control Sintético"
output: html_notebook
---

<br><br><br><br><br><br>


<p style="text-align:center;">
*La siguiente entrada es una recopilación de algunos documentos (de programación que encontré), así como el Abadie (2021) como guía en teoría de control sintetico. Con esto no pretendo escribir un documento de carácter pedagógico. Atiende tus clases de macro.*
</p>

<p style="text-align:center;">
-C
</p>

<br><br><br>



### *1. Introducción*
<br>

Los métodos de control sintetico han sido ampliamente aplicados en investigación empirica dentro de la economía y otros disciplinas de las ciencias sociales. El control sintetico, bajo las condiciones apropiadas produce ventajas sustanciales respecto de otros métodos. Sin embargo, la validez del método depende de importantes requerimientos empiricos. Las aplicaciones superficiales que ignoren el contexto de la investigación empírica o las carácteristicas necesarias que los datos deben seguir, podrían generar estimaciones equivocadas.

<br><br><br>


### *2. Aspectos formales del método de control sintético*
<br>

#### *2.1 Escenario:* 
<br>

Considere el escenario en el que una unidad agregada (un estado, un país o un distrito escolar) es expuesto a un evento o intervención de interés. En concordancia con la literatura de evaluación de programas en economía, los términos "tratado" y "no tratado" se referirán a unidades expuestas y no expuestas a una intervención o evento de interés. 

El método de control sintético fue originalmente propuesto en Abadie y Gardeazabal (2003) con el objetivo de estimar los efectos de intervenciones agregadas, esto es, intervenciones implementadas a un nivel agregado y que afectan un número pequeño de grandes unidades en algún *outcome* de interés.

El método de control sintético está basado en la idea de que, cuando las unidades de observación son un némero pequeño de entidades agregadas, una combinación de unidades no tratadas producen una comparación más apropiada que cualquier unidad por si sola. 

***Este método lo que trata de hacer es estimar las consecuencias de una intervención en la unidad de tratamiento, tomando las unidades de control para contruir una secuencia de sintetica del contrafactual en la unidad de interés en ausencia del tratamiento.***

<br>

* Suponga que obtiene datos para $J+1$ unidades: $j=1,2,...,J+1$. Sin pérdida de generalidad, supondremos que la primera unidad ($j=1$) es la unidad de tratamiento, esto es, la unidad afectada por la politica de intervención de interés. 

* El *donor pool* que es el conjunto de $J$ comparaciones potenciales $j=2,3,...,J+1$, es una colección de unidades no tratadas que no son afectadas por la política de intervención. 

* Suponga que tenemos datos para hasta $T$ periodos y que los primeros $T_{0}$ periodos son los periodos previos a la intervención.

* Para cada unidad $j$, y cada tiempo $t$, observamos el *outcome* de interés $Y_{jt}$

* Observamos también $k$ predictores de $Y_{jt}$ para los que se podrían incluir observaciónes previas a la intervención y que no son afectados por la intervención. 
 
* Para cada unidad $j$ y tiempo $t$, definimos $Y_{jt}^{N}$ la respuesta potencial sin la intervención

* $Y_{1t}^{I}$ representa la respuesta potencial de la unidad tratada ante la intervención.

* El efecto de la intervención de interés para la unidad afectada en los periodos $t>T_{0}$ es
$$\tau_{1t}=Y_{1t}^{I}-Y_{1t}^{N} \tag{1}$$
Dado que la unidad $j=1$ es la que se expone a la intervención, entonces, obsevamos $Y_{1t}=Y_{1t}^{I}$ para $t>T_{0}$. Por lo que el reto será estimar correctamente $Y_{1t}^{N}$, que es, la trayectoria potencial de $Y_{1t}$ en ausencia de la intervención. 

.
.
.




<br><br><br>


