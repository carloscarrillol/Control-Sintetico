---
title: "Control Sintético"
output: html_notebook
---

<br><br><br>
 
<p style="text-align:center;">
*Quiero que quede claro que no tengo idea de lo que estoy apunto de escribir, únicamente es una compilación de documentos que he encontrado sobre modelado de control sintético. Por lo tanto, descubriremos juntos y al mismo tiempo (eso no es verdad) que hace cada una de las siguientes lineas. Por favor no juzgues, tuve la decencia de advertirte.*
</p>

<p style="text-align:center;">
-C
</p>

<br><br><br><br><br><br>

Para el modelado de control sintético utilizarémos dos métodos con dos paqueterias distintas el primero 
`Synth` de Jens Hainmueller and Alexis Diamond de las univesidades Stanford y Harvard, respectivamente. La documentación al respecto se encuentra [aqui](https://cran.r-project.org/web/packages/Synth/Synth.pdf. ) 

La segunda paquetería, `tidysynth` que esta construida sobre la paquetería anterior. Hasta donde entiendo, la creación de esta paqueteria le corresponde a Eric Dunford de Meta y puedes consultar el `readme` completo [aqui](https://github.com/edunford/tidysynth#readme)

<br><br><br>

### Paquetería `Synth`

Lo primero será instalar la librería:
```{r}
library(Synth) 
```

Una vez cargada la librería podremos acceder a la base de datos panel mediante el siguiente comando:
```{r}
data("synth.data")
```
que es un data frame con datos por año desde 1980 hasta el año 2000, tanto para el grupo de control ($\textit{donor pool}$), como para la unidad de tratamiento (`treated.region`).

La organización de la base de datos anterior está determinada de la siguiente manera:

* `unit.num` es el identificador de cada unidad que se compone de un vector de 8 entradas sin repetición, es decir, $j=\{2,  7, 13, 17, 29, 32, 36, 38\}$, siendo la unidad de tratamiento la unidad $j=7$,

* `year` es un vector numérico con los identificadores de cada año para $t=1980,1981,...,2000$ para cada unidad,

* `name` contiene el nombre de las regiones,

* `Y` es la serie para el $\textit{outcome}$ de interés,

* las columnas `X1`,`X2` y `X3` son los predictores de `Y`.
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
.
.                                                                                                                                                                                                                               
.                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
