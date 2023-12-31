<br><br><br>


<div align="center">
  <h1>Control Sintético</h1>
</div>
 <br><br><br>
<div align="center">
  <p><em> Quiero que quede claro que no tengo idea de lo que estoy apunto de escribir, únicamente es una compilación de documentos que he encontrado sobre modelado de control sintético. Por lo tanto, descubriremos juntos y al mismo tiempo (eso no es verdad) que hace cada una de las siguientes lineas. Por favor no juzgues, tuve la decencia de advertirte. </em></p>
</div>
<div align="center">
  <p>-C</p>
</div>


<br><br><br><br><br><br>


Para el modelado de control sintético utilizarémos dos métodos con dos paqueterias distintas el primero 
`Synth` de Jens Hainmueller and Alexis Diamond de las univesidades Stanford y Harvard, respectivamente. La documentación al respecto se encuentra [aqui](https://cran.r-project.org/web/packages/Synth/Synth.pdf. ) 

La segunda paquetería, `tidysynth` que esta construida sobre la paquetería anterior. Hasta donde entiendo, la creación de esta paqueteria le corresponde a Eric Dunford de Meta y puedes consultar el `readme` completo [aqui](https://github.com/edunford/tidysynth#readme)

<br><br><br>

### Paquetería `Synth`

 [Aquí](https://rpubs.com/CarlosCarrillol/1099110) está el código corrido en R-pubs

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


![00001c](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/88dc25f1-dc05-4b3f-b7ac-d3cfbc43c9e0)


![000022](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/4987cd22-b793-4d9c-98f0-f17a7db6e840)


### Paquetería `tidysynth`

Ahora vamos a trabajar con la libreria `tidysynth`, lo pimero que hay que hacer es instalar la libreria:


```{r}
library(tidysynth)
```


La base de datos que vamos a utilizar proviene de Abadie et al. (2010). La base de datos se compone de la siguiente manera:


* La columna `state` de clase *"character*" contiene los nombres de los distintos estados de Estados Unidos,

* la columna `numeric` de clase "*numeric*" contiene el año en que se realizan las observaciones desde el año 1970 al año 2000,

* la columna `cigsale` de clase "*numeric*" contiene el total de la venta de cigarrillos per-capita por estado,

* la columna `lnincome` de clase "*numeric*" contiene el logaritmo del ingreso personal de cada estado

* la columna `beer` de clase "*numeric*" contiene el consumo de cerveza per-capita

* la columna `age15to24` de clase "*numeric*" contiene contiene el porcentaje de la población entre 15 y 24 años

* la columna `retprice` de clase "*numeric*" contiene el precio promedio de *retail* de los cigarrillos.

En este ejemplo, el *outcome* de interés es la venta de cigarrillos per-capita, y la unidad de tratamiento es el estado de California, mientras que las unidades de conrrol serán el resto de los estaods de Estados Unidos. Lo que se intenta estimar es la trayectoria sintética de la venta de cigarrillos en California de no haberse aplicado el tratamiento, que es, la llamada *Proposition 99* que consistió en un aumento de 25 centavos por paquete a través de un impuesto. 

```{r}
data("smoking")
```

Para preparar los datos como en la paquetería `Synth` utilizamos la funciónm `synthetic_control()` de la manera siguiente, la matriz predictores $X_{0}$ y $X_{1}$ con la función `generate_predictors()`, y generar los ponderadores con la función `generate_controls()`. Dado que la paqueteria es una implementación `tidy` la concatenación de las funciones se hace a través del comando `%>%`, como se muestra a continuación:


```{r}
smoking_out <-
  
  smoking %>%                                      #Este es el data frame que vamos a utilizar
  synthetic_control(outcome = cigsale,             #El outcome de interés
                    unit = state,                  #Vamos a utilizar la columna state como indice de las unidades
                    time = year,                   #Con indice de tiempo de la columna year
                    i_unit = "California",         #La unidad de tratamiento j=1
                    i_time = 1988,                 #El periodo de tratamiento T0
                    generate_placebos=T            #Generaremos placebos para hacer inferencia.
                    ) %>%
  
  # Vamos a generar los predictores agregados para ajustar los ponderadores:
  # promedio del logaritmo del ingreso, el precio de los cigarrillos y la 
  # prporcion de la población entre 15 y 24 años de 1980 - 1988
  
  generate_predictor(time_window = 1980:1988,                 # Los periodos hasta T0=1988
                     ln_income = mean(lnincome, na.rm = T),   #log del ingreso
                     ret_price = mean(retprice, na.rm = T),   #el precio de los cigarrillos
                     youth = mean(age15to24, na.rm = T)) %>%  #proporcion de la pob entre 15 - 24 años
  
  # promedio del consumo de cerveza en el donnor pool de 1984 - 1988
  generate_predictor(time_window = 1984:1988,
                     beer_sales = mean(beer, na.rm = T)) %>%
  
  # las ventas de cigarrillos atrasadas como predictores especiales
  generate_predictor(time_window = 1975,
                     cigsale_1975 = cigsale) %>%
  generate_predictor(time_window = 1980,
                     cigsale_1980 = cigsale) %>%
  generate_predictor(time_window = 1988,
                     cigsale_1988 = cigsale) %>%
  
  
  # genera los ponderadores
  generate_weights(optimization_window = 1970:1988, # el periodo para optimización de los ponderadores
                   margin_ipop = .02,sigf_ipop = 7, bound_ipop = 6 # opciones de optimización
  ) %>%
  
  # Generar el control sintético
  generate_control()
```

Para graficar las trayectorias de las series observadas y la serie sintética del problema anterior utilizamos el siguiente comando:

```{r}
smoking_out %>% plot_trends() 
```
![000014](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/313a5ec2-8f61-4de0-abc0-e6708a1343a1)


Además, podemos graficar las diferencias absolutas entre la serie observada y la serie sintética:

```{r}
smoking_out %>% plot_differences()
```
![000010](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/9969f3e8-f6fb-4b3a-92ac-c303f6bab8e6)


Los siguientes histogramas muestral la ponderación que el programa le da a las diferentes unidades, siendo Utah el que recibe mayor ponderación y Conecticut el que menos pero estrictamente positivo. Además, del lado derecho se muestran las ponderaciones para las diferntes variables en la matriz de predictores para todas las unidades $[X_{0}:X_{1}]$

Recoremos que tanto este método como el de la paquetería `Synth` generan los pesos bajo las siguientes condiciones:

$$\sum_{j=2}^{J+1}\omega_{j}=1$$
 y 
$$\omega_{j}\geq 0 \quad \text{con} \quad j=2,...,J+1$$
```{r}
smoking_out %>% plot_weights()
```

![000010](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/5ebb76d4-c7a1-4888-873a-a02d26f4fa63)

De acuerdo con Abadie et. al., (2010) el método de control sintético produce ponderadores iguales a cero para la mayoría de las unidades del *donor pool*.

Otra manera de presentar los datos es através de la siguiente tabla:

```{r}
smoking_out %>% grab_balance_table()
```

variable     | California | synthetic_California | donor_sample |
-------------|------------|----------------------|--------------|
ln_income    | 10.0765586 |	  9.8534864          |	 9.8291968	|
ret_price    | 89.4222234 |	 89.3908482	         |  87.2660819	|
youth        |	0.1735324 |	  0.1736054          |	 0.1725101	|
beer_sales   | 24.2800003 |	 24.2224860          |	23.6552632	|
cigsale_1975 |127.0999985 |	126.9914177          | 136.9315790	|
cigsale_1980 |120.1999969 | 120.2204841	         | 138.0894737	|
cigsale_1988 | 90.0999985 |	 91.3888620	         | 113.8236837	|

En la tabla se enlistan las diferentes variables, tanto para el *outcome* de interés como para sus predictores, para la unidad de tratamiento, la unidad sintética y la muestra de donantes.



### Inferencia

En cuanto a la inferencia, el método se basa en repetir el método para cada donante en el grupo de donantes exactamente de la misma manera que se hizo para la unidad tratada, es decir, generando controles sintéticos de placebo. Al establecer `generate_placebos = TRUE` al inicializar la tubería 'synth' con `synthetic_control()`, los casos de placebo se generan automáticamente al construir el control sintético de interés. Esto facilita la exploración de cuán única es la diferencia entre la unidad observada y la unidad sintética en comparación con los placebos

```{r}
smoking_out %>% 
  plot_placebos()
```

![000011](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/396739f0-94ac-4579-8ffe-b5708d9f0ae3)


Ten en cuenta que la función `plot_placebos()` automáticamente elimina cualquier placebo que se ajuste mal a los datos en el período previo a la intervención. La razón para hacerlo es puramente visual: esas unidades tienden a distorsionar la escala al trazar los placebos. Para realizar la eliminación, la función examina el error cuadrático medio de predicción (MSPE) en el período previo a la intervención (es decir, una métrica que refleja cuán bien se ajusta el control sintético a la serie de tiempo de resultados observados en el período previo a la intervención). Si un control de placebo tiene un MSPE que es dos veces mayor que el caso objetivo (por ejemplo, "California"), entonces se elimina. Para desactivar este comportamiento, establece `prune = FALSE`.

```{r}
smoking_out %>% plot_placebos(prune = FALSE)
```

![000010](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/9966cd60-9a79-4b87-b623-0569f3b3e522)


Finalmente, Adabie et al. en 2010 describen una manera de construir los valores p exactos de Fisher dividiendo el MSPE posterior a la intervención por el MSPE anterior a la intervención y luego clasificando todos los casos en orden descendente según esta proporción. Se construye un valor p tomando la clasificación entre el total de casos. La idea subyacente es que si el control sintético se ajusta bien a la serie de tiempo observada (bajo MSPE en el período previo) y se desvía en el período posterior (alto MSPE en el período posterior), entonces hay un efecto significativo debido a la intervención. Si la intervención no tuvo efecto, entonces el período posterior y el período previo deberían continuar mapeándose bastante bien, lo que daría como resultado una proporción cercana a 1. Si las unidades de placebo se ajustan de manera similar a los datos, entonces no podemos rechazar la hipótesis nula de que la intervención no tuvo efecto.

Esta proporción se puede representar fácilmente utilizando `plot_mspe_ratio()`, lo que ofrece información sobre la rareza del caso en el que la intervención realmente ocurrió.

```{r}
smoking_out %>% plot_mspe_ratio()
```

![000012](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/7305727a-83b8-477a-8241-ee07e7176553)




Un segundo ejemplo:


```{r}
library(Synth)
data("synth.data")

synth.data <- filter(synth.data, 
                     year <= 1996)
  
synth.data.out <- 
  synth.data %>% 
  synthetic_control(outcome = Y,
                    unit = name,
                    time = year,
                    i_unit = "treated.region",
                    i_time = 1991,
                    generate_placebos = T
                    ) %>%
  generate_predictor(time_window = 1980:1991,
                   x1 = mean(X1, na.rm = T),
                   x2 = mean(X2, na.rm = T),
                   x3 = mean(X3, na.rm = T)
                   )  %>%
  generate_predictor(time_window = 1991,
                     Y_91 = Y) %>%
  generate_predictor(time_window = 1985,
                     Y_85 = Y) %>%
  generate_predictor(time_window = 1980,
                     Y_80 = Y) %>%
  generate_weights(optimization_window = 1970:1991,
                   margin_ipop = .02, sigf_ipop = 7, 
                   bound_ipop = 6
                   ) %>%
  generate_control()

synth.data.out %>%
  plot_trends()
```

![000035](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/89212be7-8fbd-42ab-ab96-2158bb8c12c5)


```{r}
synth.data.out %>%
  plot_differences()
```
![000002](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/99994a5b-21d4-4e0d-9957-5dbd5c3d89f1)

```{r}
synth.data.out %>%
  plot_weights()
```

![000002](https://github.com/carloscarrillol/Control-Sintetico/assets/122711749/b706fbe3-a460-4804-a5d5-fbe8f7220b9b)

```{r}
synth.data.out %>%
  grab_balance_table()
```


variable | treated.region | synthetic_treated.region | donor_sample |
---------|----------------|--------------------------|--------------|
x1	     |  0.267079      |  0.2578198               |	0.2587789	  |
x2	     | 16.200000      |	16.2031799	             | 16.3297619	  |
x3	     | 21.725000      | 22.3468529	             | 22.8750000   |	
Y_91	   |115.000000	    |115.0197237	             |117.3714273	  |
Y_85	   |128.800003	    |128.1305395	             |132.4571424   |	
Y_80	   |134.000000      |134.0382530	             |145.7571422   |	










