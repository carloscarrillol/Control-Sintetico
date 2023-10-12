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
                                                                                                                                                                                                                               
  

Vamos a tomar los valores medios de cada predictor para todos los años, es decir:

$$\frac{1}{T}\sum_{t=1}^{T}x_{ijt}$$
es la entrada de la fila $i$ y la columna $j$ de la matriz $X_{0}$, que es la matriz de predictores para las unidades $j=1,2,...,J$ e $i=1,2,3$


En este ejemplo la matriz de predictores $X_{1}$ está determinada como sigue:
```{r}
X1 <- filter(synth.data, name == "treated.region") %>% 
  select("X1","X2","X3")
X1.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X1.1)){
  X1.1[,i]<-mean(X1[,i],na.rm = T)
}

X1.1
```
mientras que la matriz de predictores con las mismas variables para $j=2,...,8$, $X_{0}$, la construimos de la siguiente manera:

```{r}
X2 <- filter(synth.data, name == "control.region.northeast") %>% 
  select("X1","X2","X3")              
X2.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X2.1)){
  X2.1[,i]<-mean(X2[,i],na.rm = T)
}
colnames(X2.1) <- colnames(X2)                    #Estas son las matrices con los valores medios por columna

X3 <- filter(synth.data, name == "control.region.southcentral") %>% 
  select("X1","X2","X3")
X3.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X3.1)){
  X3.1[,i]<-mean(X3[,i],na.rm = T)
}


X4 <- filter(synth.data, name == "control.region.west") %>% 
  select("X1","X2","X3")
X4.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X4.1)){
  X4.1[,i]<-mean(X4[,i],na.rm = T)
}


X5 <- filter(synth.data, name == "control.region.southeast") %>% 
  select("X1","X2","X3")
X5.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X5.1)){
  X5.1[,i]<-mean(X5[,i],na.rm = T)
}


X6 <- filter(synth.data, name == "control.region.central") %>% 
  select("X1","X2","X3")
X6.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X6.1)){
  X6.1[,i]<-mean(X6[,i],na.rm = T)
}


X7 <- filter(synth.data, name == "control.region.south") %>% 
  select("X1","X2","X3")
X7.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X7.1)){
  X7.1[,i]<-mean(X7[,i],na.rm = T)
}


X8 <- filter(synth.data, name == "control.region.east") %>% 
  select("X1","X2","X3")
X8.1 <- matrix(rep(0,3),ncol=3) 
for (i in  1:ncol(X8.1)){
  X8.1[,i]<-mean(X8[,i],na.rm = T)
}


X0 <- rbind(X2.1,X3.1,X4.1,X5.1,X6.1,X7.1,X8.1)
X0
```

                                                                                                                                                                                                                               
.
.                                                                                                                                                                                                                  
.                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
