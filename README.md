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


#### *2.3 Estimación:*

Los estudios de casos comparativos (*Comparative Case Studies*) intentan reproducir $Y_{1t}^{N}$ utilizando una unidad no afectada o un número pequeño de unidades que tienen características similares a la unidad afectada. Cuando los datos consisten en entidades agregadas como regiones o países, es complicado encontrar regiones particulares no afectadas que provean de comparaciones adecuadas.

Por otro lado, el método de control sintético se basa en la idea de que la combinación de unidades del *donor pool* podrían aproximar las características de la unidad afectada sustancialmente mejor que cualquier unidad por sí sola. Formalmente, un control sintético se representa como un vector de $J\times 1$ ponderadores $W=(\omega_{2},...,\omega_{J+1})^{T}$. Dado el vector $W$, los estimadores de control sintético de $Y_{1t}^{N}$ y $\tau_{1t}$ son, respectivamente,

$$ \hat{Y}_{1t}^{N}=\sum_{j=2}^{J+1}\omega_{j}Y_{jt} \tag{2} $$
 y
 
 $$\hat{\tau}_{1t}=Y_{1t}-\hat{Y}_{1t}^{N}\tag{3}$$

Para evitar la extrapolación de las series necesitamos que

* $\omega_{j}\geq 0$ para toda $j=2,...,J+1$
* $\sum_{j=2}^{J+1}\omega_{j}=1$

***Note que para que las restricciones anteriores podrían satisfacerse únicamente si las variables en los datos están rescalados adecuandamente para corregir las diferencias en el tamaño de las unidades***

A continuación se presentan algunos ejemplos para la elección de los ponderadores:

1) Un control sintético que asigna igual peso a cualquier unidad $\omega_{j}=1/J$ resulta en el siguiente estimador para $\tau_{1t}$
$$\hat{\tau}_{1t}=Y_{1t}-\frac{1}{J}\sum_{j=2}^{J+1}Y_{jt}\tag{4}$$

que genera una estimación como un promedio simple de todas las unidades en el *donor pool*.

2) Promedio ponderado por población:
$$\hat{\tau}_{1t}=Y_{1t}-\sum_{j=2}^{J+1}\omega_{j}^{pop}Y_{jt} \tag{5}$$
donde $\omega_{j}^{pop}$ es la población de la unidad $j$ dividido entre el total de la población en el *donor pool*.

3) Abadie, et.al., (2003) y Abadie, et. al., (2010) proponen elegir el control sintético $W=(\omega_{2}^{*},...,\omega_{J+1}^{*})^{T}$ que minimice:

$$\| X_{1}-X_{0}W\| = \left(\sum_{h=1}^{k}\upsilon_{h}\left(X_{h1}-\omega_{2}X_{h2}-\cdots - \omega_{J+1}X_{hJ+1}\right)^{2}\right)^{1/2} \tag{6}$$

$$\textit{s.a} \quad \quad \omega_{j}\geq 0 \quad j=2,...,J+1\quad ; \quad \quad \sum_{j=2}^{J+1}\omega_{j}=1$$
por lo que el efecto estimado de tratamiento de la unidad tratada en el tiempo $t=T_{0}+1,T_{0}+2,...,T$ es 
$$\hat{\tau}_{1t}=Y_{1t}-\sum_{j=2}^{J+1}\omega_{j}^{*}Y_{jt} \tag{7}$$
Las constantes positivas $(\upsilon_{1},...,\upsilon_{k})=V$ en la ecuación (6) reflejan la importancia relativa control sintético en la reproducción de los valores de cada uno de los $k$ predictores para la unidad tratada, $X_{11},..,X_{k1}$.

Una selección simple de $\upsilon_{h}\in V$ es la inversa de la varianza de $X_{h1},...,X_{hJ+1}$ que reescale $\left[X_{0}:X_{1}\right]$ de tal forma que tenga varianza unitaria.

<br>
<p style="text-align:center;">
*Aún hay más sobre la elección de los ponderadores, pero creo que lo pondré en un update después*
</p>
<br>


#### *2.3 Sesgo Limitado*

Abadie, et. al., (2010) estudia las propiedades del sesgo de los estimadores generados por el método de control sintético. Para los casos en que $Y_{jt}^{N}$ es genrado por 1) un modelo lineal, o 2) un modelo de vector autorregresivo (VAR). Muestra que bajo ciertas condiciones los estimadores de control sintético generados por un modelo VAR son insesgados, mientras que proporcionan un límite para el sesgo generado por la estimación con el modelo lineal.


<br>
<p style="text-align:center;">
*Esta nota se restringe al estudio del modelo de factor lineal que puede verse como una generalización del modelo de diferencias en diferencias. En otra entrada añadiré lo relatico a los modelos VAR que encuentre en Abadie, et. al. (2010)*
</p>
<br>

Considere el siguiente modelo de factor lineal para $Y_{jt}^{N}$
$$Y_{jt}^{N}=\delta_{t}+\theta_{t}Z_{j}+\lambda_{t}\mu_{j}+\varepsilon_{jt}\tag{8}$$
donde,

* $\delta_{t}$ es la tendencia lineal de la serie,
* $Z_{j}$ y $\mu_{j}$ son predictores observados y no observados de $Y_{jt}^{N}$, respectivamente, con coeficientes $\theta_{t}$ y $\lambda_{t}$, y
* $\varepsilon_{jt}$ es un choque transitorio con media cero.

Una caracterización del sesgo de los estimadores de control sintetico generados por la ecuación (8) que reproduce las características de la unidad tratada se presenta como sigue:

* Sea $X_{1}$ el vector que incluye $Z_{1}$ y los outcomes previos a la intevención (i.e., $Y_{1t}^{N}$ para $t<T_{0}$) para la unidad tratada,
* Sea $X_{0}$ la matriz que recolecta las mismas variables para las unidades no afectadas.
* Suponga que $X_{1}=X_{0}W^{*}$, que significa que el control sintético $W^{*}$ es capaz de reproducir las características de la unidad tratada.
* Entonces, el sesgo de $\hat{\tau}_{it}$ está controlado por la razon entre la escala de los choques transitorios $\varepsilon_{it}$ y el numero de periodos previos a $T_{0}$. 
* Por lo tanto, una recomendación sería obtener la mayor cantidad de observaciones previos al periodo de intervención.



.
.
.




<br><br><br>


