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

Ahora vamos a preparar de manera correcta los datos utilizando la función `dataprep`:

```{r}
dataprep.out <- dataprep(foo = synth.data,                        #Cargar los datos
                         predictors = c("X1", "X2", "X3"),        #Matriz de predictores 
                         predictors.op = "mean",                  #El operador que se utilizará en los predictores 
                         dependent = "Y",                         #Outcome
                         unit.variable = "unit.num",              
                         time.variable = "year",                  #Frecuencia temporal de la base
                         special.predictors = list(               #Predictores numéricos adicionales
                           list("Y", 1991, "mean"),               #que asocia periodos previos al
                           list("Y", 1985, "mean"),               #tratamiento y operadores "mean".
                           list("Y", 1980, "mean")),
                         treatment.identifier = 7,                #id de la unidad de tratamiento
                         controls.identifier = c(2, 13, 17, 
                                                 29, 32, 36, 38),  #id de las unidades de control
                         time.predictors.prior = c(1980:1989),    #Un vector numérico que contiene los periodos previos al tratamiento sobre los que el predictor será promediado.
                         time.optimize.ssr = c(1980:1989),        #Un vector numérico que identifica los periodos de la variable dependiente sobre los cuales la función de perdida se minimiza (i.e., la minimización de la suma de los residuos al cuadrado entre la unidad de tratamiento y la unidad de control.)
                         unit.names.variable = "name",            #Nombre de las unidades de control
                         time.plot = 1980:1996)                   #Un vector que identifica los períodos durante los cuales se trazarán los resultados con gaps.plot y path.plot.

```
Note que para generar las matrices $X_{0}$ tomamos los promedios por año de las observaciones para cada variable $X_{j}$ con $j=1,2,3$ y las variables especiales son simplemente las observaciones para $Y$ de los años 1980, 1985 y 1990. 
                                                                                                                              
                                                                                                                                                                                                                               
 Para generar el control sintetico, es decir el vector de ponderadores $W=(\omega_{2},...,\omega_{J+1})^{T}$ tal que 
 
 $$Y_{1t}^{N}=\sum_{j=2}^{J+1} \omega_{j} Y_{jt}$$


donde $Y_{1t}^{N}$ es el outcome de interés en ausencia de la intevención, para $t>T_{0}$, el periodo en el que ocurre la intervención.
 
 ```{r}
synth.out <- synth(dataprep.out)            #Genera el vector de 6x1 entradas del control sintetico para cada predictor

round(synth.out$solution.w,2)               #Redondea y presenta las variables a dos caracteres después del punto decimal


gaps<- dataprep.out$Y1plot-(
        dataprep.out$Y0plot%*%synth.out$solution.w)  #Esta ecuación es simplemente el tau_1t para todos los periodos.
```
Estos ponderadores son tales que 1) $\sum_{j} \omega_{j}=1$ y 2) $\omega_{j}\leq 0$ para $j=2,...,J+1$.


Como habíamos convenido en la entrada de teoría de control sintético uno de los requerimientos para valorar el vector $W^{*}$ es la regla del *convex hull*, es decir

$$X_{1}-X_{0}W^{*}\approx 0$$

```{r}
t(X1.1)-(t(X0)%*%synth.out$solution.w)
```

De acuerdo con la teoría, para obtener mayor aproximación tendriamos que utilizar más información para antes del periodo de intervención.

Finalmente, para presentar de manera gráfica los datos utilizamos las funciones `path.plot()` para graficar las trayectorias observadas y las sintéticas, y `gaps.plot()` para gráficar las diferencias entre la serie observada y la sintética en todo el horizonte temoporal.

```{r}
synth.tables <- synth.tab(
      dataprep.res = dataprep.out,
      synth.res = synth.out)                       #Presenta los resultados del modelo en una tabla
print(synth.tables)


path.plot(dataprep.res = dataprep.out,
          synth.res = synth.out,
          Main = "Trayectorias de control sintético")                  #Grafica las trayectorias de la unidad tratada y su trayectoria sintética
abline(v = 1991, col = "red", lty = 2)
text(1991, max(y), "Periodo de \n Tratamiento (T0) ",
     pos = 2, col ="red")


gaps.plot(dataprep.res = dataprep.out,
          synth.res = synth.out,
          Main = "Brechas del cotrol sintético")                  #Grafica las discrepancias entre la unidad tratada y su trayectoria sintética
abline(v = 1991, col = "red", lty = 2)
text(1991, max(y), "Periodo de \n Tratamiento (T0) ",
     pos = 2, col ="red")

```




.
.                                                                                    
.                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
                                                                                                                                                                                                                               
