<properties
	pageTitle="Extender el experimento con R | Microsoft Azure"
	description="Cómo extender la funcionalidad de Estudio de aprendizaje automático de Azure mediante el lenguaje R con el módulo Ejecutar script de R."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="garye" />


#Extender el experimento con R

Puede extender la funcionalidad de Estudio de aprendizaje automático mediante el lenguaje R con el módulo [Ejecutar script de R][execute-r-script].

Este módulo acepta varios conjuntos de datos de entrada y genera un solo conjunto de datos como salida. Puede escribir un script de R en el parámetro **Script de R** del módulo [Ejecutar script de R][execute-r-script].

Para tener acceso a cada puerto de entrada del módulo, se usa un código similar al siguiente:

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Enumeración de todos los paquetes actualmente instalados

La lista de paquetes instalados puede cambiar. Para obtener la lista completa, ejecute las siguientes líneas en el módulo [Ejecutar script de R][execute-r-script] para enviar la lista al conjunto de datos de salida:

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

Para ver la lista de paquetes, conecte un módulo de conversión, como [Convertir a CSV][convert-to-csv] con la salida del módulo [Ejecutar script de R][execute-r-script], ejecute el experimento y, a continuación, haga clic en la salida del módulo de conversión y seleccione **Descargar**.

##Importación de paquetes

También puede importar paquetes que todavía no están instalados desde un repositorio de Estudio de aprendizaje automático de ensayo si usa los siguientes comandos en el módulo [Ejecutar script de R][execute-r-script] y el archivo de paquetes comprimido:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

donde el archivo `my_favorite_package.zip` contiene el archivo ZIP del paquete.

##Lista de paquetes instalados

Para su comodidad, la siguiente tabla proporciona la lista de paquetes incluidos en la versión actual.

Para obtener la lista completa de paquetes actualmente disponibles, consulte la sección anterior titulada **Enumeración de todos los paquetes actualmente instalados**. La documentación actual sobre R se encuentra disponible [aquí](http://cran.r-project.org/manuals.html).

- [Módulos de R que comienzan de la A a la E]
- [Módulos de R que comienzan de la F a la L]
- [Módulos de R que comienzan de la M a la R]
- [Módulos de R que comienzan de la S a la Z]

[Módulos de R que comienzan de la A a la E]: #r-modules-beginning-with-a-through-e
[Módulos de R que comienzan de la F a la L]: #r-modules-beginning-with-f-through-l
[Módulos de R que comienzan de la M a la R]: #r-modules-beginning-with-m-through-r
[Módulos de R que comienzan de la S a la Z]: #r-modules-beginning-with-s-through-z


###Módulos de R que comienzan de la A a la E

| Nombre del paquete | Descripción del paquete |
| ------------ | ------------------- |
| abc | Herramientas para Cálculo bayesiano aproximado (ABC) |
| abind | Combinar matrices multidimensionales |
| actuar | Funciones actuariales |
| ade4 | Análisis de datos ecológicos: método exploratorio y método euclideano en ciencias medioambientales |
| AdMit | Mezcla adaptable de distribuciones t de Student |
| aod | Análisis de datos sobredispersos |
| ape | Análisis de filogenética y evolución |
| approximator | Predicción bayesiana de códigos informáticos complejos |
| arm | Análisis de datos mediante modelos jerárquicos y de varios niveles y regresión |
| arules | Minería de reglas de asociación y conjuntos de elementos frecuentes |
| arulesViz | Visualización de reglas de asociación y conjuntos de elementos frecuentes |
| ash | Rutinas de ASH de David Scott |
| assertthat | Aserciones previas y posteriores simples |
| AtelieR | Una GUI de GTK para enseñar los conceptos básicos en inferencia estadística y realizar pruebas bayesianas elementales |
| BaBooN | Coincidencia de la media predictiva de arranque bayesiano: imputación única o múltiple para datos discretos |
| BACCO | Análisis bayesiano de la salida de código informático (BACCO) |
| BaM | Funciones y conjuntos de datos para libros de Jeff Gill |
| bark | Kernels de regresión aditiva bayesiana |
| BAS | Modelo bayesiano promedio con muestreo adaptivo bayesiano |
| base | El paquete base de R |
| BayesDA | Funciones y conjuntos de datos del libro Bayesian Data Analysis (Análisis bayesiano de datos) |
| bayesGARCH | Estimación bayesiana del modelo GARCH(1,1) con innovaciones t de Student |
| bayesm | Inferencia bayesiana para marketing/microeconometría |
| bayesmix | Modelos bayesianos mixtos con JAGS |
| bayesQR | Regresión bayesiana centil |
| bayesSurv | Regresión bayesiana de supervivencia distribuciones de efectos aleatorios y errores flexibles |
| Bayesthresh | Modelos bayesianos de efectos mixtos para datos categóricos |
| BayesTree | Métodos bayesianos para modelos basados en árbol |
| BayesValidate | Paquete BayesValidate |
| BayesX | Utilidades de R que acompañan el paquete de software BayesX |
| BayHaz | Funciones de R para la estimación bayesiana de la tasa de riesgo |
| bbemkr | Estimación bayesiana del ancho de banda para la regresión multivariante de kernel con error gaussiano |
| BCBCSF | Clasificación bayesiana corregida por sesgo con características seleccionadas |
| BCE | Estimador bayesiano de composición: estimación de la composición de ejemplo (taxonómica) a partir de datos de marcadores biológicos |
| bclust | Clúster bayesiano mediante el modelo jerárquico Spike-and-Slab, adecuado para agrupar datos altamente dimensionales en clústeres |
| bcp | Un paquete para realizar un análisis bayesiano de los problemas de punto de cambio |
| BenfordTests | Pruebas estadísticas para evaluar el cumplimiento con la ley de Benford |
| bfp | Polinomios fraccionarios bayesianos |
| BH | Aumentar los archivos de encabezado de C++ |
| bisoreg | Regresión bayesiana isotónica con polimonios de Bernstein |
| bit | Una clase de vectores de valores booleanos de 1 bit |
| bitops | Operaciones bit a bit |
| BLR | Regresión lineal bayesiana |
| BMA | Modelo bayesiano promedio |
| Bmix | Muestreo bayesiano para mezclas stick-breaking |
| BMS | Biblioteca de modelo bayesiano promedio |
| bnlearn | Aprendizaje bayesiano de estructura de red, aprendizaje bayesiano de parámetro y aprendizaje de inferencia |
| boa | Programa de análisis bayesiano de salida (BOA) para MCMC |
| Bolstad | Funciones de Bolstad |
| boot | Funciones de arranque (originalmente de Angelo Canty para S) |
| bootstrap | Funciones para el libro An Introduction to the Bootstrap (Una introducción al arranque) |
| bqtl | Kit de herramientas de asignación bayesiana de QTL |
| BradleyTerry2 | Modelos de Bradley-Terry |
| brew | Marco de plantilla para la generación de informes |
| brglm | Reducción del sesgo en modelos lineales generalizados de respuesta binomial |
| bspec | Inferencia bayesiana espectral |
| bspmma | bspmma: modelos bayesianos semiparamétricos para metaanálisis |
| BVS | Selección de variante bayesiana: técnicas de incertidumbre del modelo bayesiano para estudios de asociación genética |
| cairoDevice | Controlador de dispositivo gráfico con suavizado de contorno multiplataforma basado en Cairo |
| calibrator | Calibración bayesiana de códigos informáticos complejos |
| car | Complemento de la regresión aplicada |
| caret | Entrenamiento de clasificación y regresión |
| catnet | Interfaz de red bayesiana de categoría |
| caTools | Herramientas: movimiento de estadísticas de ventana, GIF, Base64, AUC de ROC, etc. |
| chron | Objetos cronológicos que pueden controlar fechas y horas |
| class | Funciones de clasificación |
| de HBase | Análisis de clúster extendido, Rousseeuw et al. |
| clusterSim | Búsqueda del procedimiento de agrupación óptima en clústeres para un conjunto de datos |
| coda | Análisis de salida y diagnóstico para MCMC |
| codetools | Herramientas de análisis de código para R |
| coin | Procedimientos de inferencia condicional en un marco de prueba de permutación |
| colorspace | Manipulación del espacio de colores |
| combinat | utilidades combinatorias |
| compiler | El paquete del compilador de R |
| corpcor | Estimación eficaz de covarianza y correlación (parcial) |
| cslogistic | Regresión logística condicionalmente especificada |
| ctv | Vistas de tareas de CRAN |
| cubature | Integración multivariante adaptativa sobre hipercubos |
| data.table | Extensión de data.frame |
| datasets | El paquete de conjuntos de datos de R |
| fecha | Funciones para controlar fechas |
| dclone | Herramientas de MCMC y clonación de datos para métodos de máximo de probabilidades |
| deal | Redes bayesianas de aprendizaje con variables mixtas |
| Deducer | Deducer: una GUI de análisis de datos para R |
| DeducerExtras | Cuadros de diálogos y funciones adicionales para Deducer |
| deldir | Triangulación de Delaunay y teselación de Dirichlet (Voronoi). |
| DEoptimR | Optimización de evolución diferencial en R |
| deSolve | Solucionadores generales de problemas de valor inicial de ecuaciones diferenciales ordinarias (ODE), ecuaciones diferenciales parciales (PDE), ecuaciones algebraicas diferenciales (DAE) y ecuaciones diferenciales con retardo (DDE) |
| devtools | Herramientas para facilitar el desarrollo del código de R |
| dichromat | Esquemas de color para dicromáticos |
| digest | Crear resúmenes de hash criptográficos de objetos de R |
| distrom | Regresión distribuida multinomial |
| dlm | Análisis bayesiano y de probabilidad de modelos lineales dinámicos |
| doSNOW | Adaptador paralelo foreach para el paquete de Snow |
| dplyr | dplyr: una gramática para la manipulación de datos |
| DPpackage | Modelado bayesiano no paramétrico en R |
| dse | Estimación de sistemas dinámicos (paquete de series temporales) |
| e1071 | Funciones auxiliares del Departamento de estadísticas (e1071), TU Wien |
| EbayesThresh | Umbral empírico de Bayes y métodos relacionados |
| ebdbNet | Estimación empírica de Bayes de redes bayesianas dinámicas |
| effects | Exhibiciones de efectos para el modelo lineal, modelo lineal generalizado, modelo logit multinomial y modelo logit proporcional-probabilidades, además de los modelos de efectos mixtos |
| emulator | Emulación bayesiana de programas informáticos |
| ensembleBMA | Previsión de probabilidad mediante conjuntos y modelo bayesiano promedio |
| entropy | Estimación de la entropía, información mutua y cantidades relacionadas |
| EvalEst | Estimación de sistemas dinámicos: extensiones |
| evaluate | Herramientas de análisis y evaluación que proporcionan más detalles que los predeterminados |
| evdbayes | Análisis bayesiano en la teoría del valor extremo |
| evora | Valores atípicos de variables epigenéticas para el análisis de predicción del riesgo |
| exactLoglinTest | Pruebas exactas de Monte Carlo para modelos logarítmicos lineales |
| expm | Exponencial de una matriz |
| extremevalues | Detección de valores atípicos univariantes |


###Módulos de R con nombres que comienzan de la F a la L

| Nombre del paquete | Descripción del paquete |
| ------------ | ------------------- |
| factorQR | Modelos de factor de regresión bayesiana centil |
| faoutlier | Métodos de detección de casos influyentes para el análisis de factores y SEM |
| fitdistrplus | Ayuda para adaptar una distribución paramétrica a datos censurados o datos no censurados |
| FME | Un entorno de modelado flexible para análisis de Monte Carlo, identificabilidad, susceptibilidad, modelado inverso |
| foreach | Construcción de bucle foreach para R |
| forecast | Funciones de previsión para modelos lineales y series temporales |
| foreign | Leer datos almacenados por Minitab, S, SAS, SPSS, Stata, Systat, Weka, dBase, ... |
| formatR | Dar formato automáticamente al código de R |
| Formula | Fórmulas de modelos extendidos |
| fracdiff | Modelos ARIMA o ARFIMA (p,d,q) diferenciados de manera fraccionaria |
| gam | Modelos aditivos generalizados |
| gamlr | Regresión gama lazo |
| gbm | Modelos generalizados de regresión aumentada |
| gclus | Agrupación de gráficos en clústeres |
| gdata | Diversas herramientas de programación en R para la manipulación de datos |
| gee | Solucionador de ecuaciones de estimación generalizadas |
| genetics | Genética de poblaciones |
| geoR | Análisis de datos geoestadísticos |
| geoRglm | geoRglm: un paquete para modelos espaciales lineales generalizados |
| geosphere | Trigonometría esférica |
| ggmcmc | Herramientas gráficas para analizar las simulaciones de Markov Monte Carlo desde la inferencia bayesiana |
| ggplot2 | Una implementación de la gramática de los gráficos |
| glmmBUGS | Modelos mixtos lineales generalizados y modelos espaciales con WinBUGS, BRugs u OpenBUGS |
| glmnet | Modelos lineales generalizados regularizados de red elástica y lazo |
| gmodels | Diversas herramientas de programación en R para ajuste de modelo |
| gmp | Aritmética de precisión múltiple |
| gnm | Modelos no lineales generalizados |
| googlePublicData | Una biblioteca de R para crear archivos de metadatos DSPL del Explorador de datos públicos de Google |
| googleVis | Interfaz entre R y Google Charts |
| GPArotation | Rotación de factores de GPA |
| gplots | Diversas herramientas de programación en R para el trazado de datos |
| graphics | El paquete de gráficos de R |
| grDevices | Los dispositivos gráficos de R y compatibilidad con colores y fuentes |
| gregmisc | Funciones varias de Greg |
| grid | El paquete de gráficos de la cuadrícula |
| gridExtra | funciones en gráficos de la cuadrícula |
| growcurves | Modelos de curva de crecimiento no paramétricos y semiparamétricos bayesianos que incluyen además varios efectos aleatorios de pertenencia |
| grpreg | Rutas de regularización para modelos de regresión con covariantes agrupadas |
| gsubfn | Utilidades para argumentos de funciones y cadenas |
| gtable | Organizar grobs en tablas |
| gtools | Diversas herramientas de programación en R |
| gWidgets | API de gWidgets para crear GUI interactivas e independientes del kit de herramientas |
| gWidgetsRGtk2 | Implementación del kit de herramientas de gWidgets para RGtk2 |
| haplo.Stats | Análisis estadístico de haplotipos con rasgos y covariantes cuando la fase de ligamiento es ambigua |
| hbsae | Estimación bayesiana jerárquica de área pequeña |
| hdrcde | Regiones de mayor densidad y estimación de densidad condicional |
| heavy | Paquete para la adaptación de valores atípicos mediante el uso de distribuciones de cola pesada |
| hflights | Vuelos que salieron de Houston en 2011 |
| HH | Análisis estadístico y visualización de datos: Heiberger y Holland |
| HI | Simulación de distribuciones admitidas por hiperplanos anidados |
| highr | Resaltado de sintaxis para R |
| Hmisc | Varios de Harrell |
| htmltools | Herramientas para HTML |
| httpuv | Biblioteca de servidores de WebSocket y HTTP |
| httr | Herramientas para trabajar con direcciones URL y HTTP |
| IBrokers | API de R para Trader Workstation de Interactive Brokers |
| igraph | Análisis y visualización de red |
| intervals | Herramientas para trabajar con puntos e intervalos |
| iplots | iPlots: gráficos interactivos para R |
| ipred | Predictores mejorados |
| irr | Diversos coeficientes de acuerdo y confiabilidad entre evaluadores |
| iterators | Construcción de iterador para R |
| JavaGD | Dispositivo gráfico de Java |
| JGR | JGR: GUI de Java para R |
| kernlab | Laboratorio de aprendizaje automático basado en kernel |
| KernSmooth | Funciones para suavizado de kernel para Wand y Jones (1995) |
| KFKSDS | Filtro, suavizador y suavizador de disturbios de Kalman |
| kinship2 | Funciones de Pedigree |
| kknn | Vecinos k más próximos ponderados |
| klaR | Clasificación y visualización |
| knitr | Un paquete de uso general para la generación de informes dinámicos en R |
| ks | Suavizado de kernel |
| labeling | Etiquetado de los ejes |
| Lahman | Base de datos de béisbol de Sean Lahman |
| lars | Regresión de ángulo mínimo, regresión lazo y regresión por etapas hacia adelante |
| lattice | Gráficos de cuadrícula |
| latticeExtra | Utilidades gráficas adicionales basadas en cuadrícula |
| lava | Modelos lineales de variable latente |
| lavaan | Análisis de variable latente |
| leaps | selección de subconjunto de regresiones |
| LearnBayes | Funciones para inferencia bayesiana de aprendizaje |
| limSolve | Solución de modelos inversos lineales |
| lme4 | Modelos lineales de efectos mixtos mediante Eigen y S4 |
| lmm | Modelos lineales mixtos |
| lmPerm | Pruebas de permutación para modelos lineales |
| lmtest | Prueba de modelos de regresión lineal |
| locfit | Estimación de densidad, probabilidades y regresión local |
| lpSolve | Interfaz para Lp\_solve v. 5.5 para solucionar programas lineales/de enteros |


###Módulos de R con nombres que comienzan de la M a la R

| Nombre del paquete | Descripción del paquete |
| ------------ | ------------------- |
| magic | crear e investigar cuadrados mágicos |
| magrittr | magrittr: un operador de canalización hacia adelante |
| mapdata | Bases de datos de mapas adicionales |
| mapproj | Proyecciones de mapas |
| maps | Dibujar mapas geográficos |
| maptools | Herramientas para leer y controlar objetos espaciales |
| maptree | Asignación, eliminación y representación gráfica de modelos de árbol |
| markdown | Presentación de reducciones para R |
| MASS | Admitir funciones y conjuntos de datos para MASS de Venables y Ripley |
| MasterBayes | Métodos de aprendizaje automático y MCMC para el análisis y la reconstrucción de Pedigree |
| Matrix | Métodos y clases de matrices dispersas y densas |
| matrixcalc | Colección de funciones para cálculos de matriz |
| MatrixModels | Modelado con matrices dispersas y densas |
| maxent | Regresión logística multinomial de baja memoria con compatibilidad para la clasificación de textos |
| maxLik | Estimación del máximo de probabilidades |
| mcmc | Cadena de Markov Monte Carlo |
| MCMCglmm | Modelos lineales mixtos generalizados de MCMC |
| MCMCpack | Paquete de cadena de Markov Monte Carlo (MCMC) |
| memoise | Funciones de memoise |
| methods | Clases y métodos formales |
| mgcv | Vehículo de cálculo GAM mixto con estimación de suavizado GCV/AIC/REML |
| mice | Imputación multivariante por ecuaciones encadenadas |
| microbenchmark | Funciones precisas de control de tiempo sub-microsegundo |
| mime | Asignar nombres de archivo a tipos MIME |
| minpack.lm | Interfaz de R para el algoritmo no lineal de cuadrados mínimos de Levenberg-Marquardt encontrado en MINPACK, más la compatibilidad con límites |
| minqa | Algoritmos de optimización sin derivadas por aproximación cuadrática |
| misc3d | Gráficos 3D varios |
| miscF | Funciones varias |
| miscTools | Herramientas y utilidades varias |
| mixtools | Herramientas para analizar modelos mixtos finitos |
| mlbench | Problemas de referencia del aprendizaje automático |
| mlogitBMA | Modelo bayesiano promedio para modelos Logit multinomiales |
| mnormt | Las distribuciones t y normales multivariantes |
| MNP | Paquete de R para ajustar el modelo Probit multinomial |
| modeltools | Herramientas y clases para modelos estadísticos |
| mombf | Factores bayesianos de momento y de momento inverso |
| monomvn | Estimación para datos t de Student y datos normales multivariantes con datos faltantes monótonos |
| monreg | Regresión monótona no paramétrica |
| mosaic | Utilidades para la enseñanza de matemáticas y estadísticas del proyecto MOSAIC (mosaic.web.org) |
| MSBVAR | Modelo de autorregresión vectorial, modelo bayesiano y modelo de cambio de Markov |
| msm | Modelo oculto de Markov y modelo de Markov de estados múltiples en tiempo continuo |
| multcomp | Inferencia simultánea en modelos paramétricos generales |
| multicool | Permutaciones de conjuntos múltiples en orden cool-lex. |
| munsell | Sistema de color de Munsell |
| mvoutlier | Detección de valores atípicos multivariantes basada en métodos sólidos |
| mvtnorm | Distribuciones t y distribuciones normales multivariantes |
| ncvreg | Rutas de regularización para modelos de regresión SCAD y MCP penalizados |
| nlme | Modelos de efectos mixtos lineales y no lineales |
| NLP | Infraestructura de procesamiento de lenguaje natural |
| nnet | Modelos logarítmicos lineales multinomiales y redes neuronales de prealimentación |
| numbers | Funciones numérico-teóricas |
| numDeriv | Derivados numéricos precisos |
| openNLP | Interfaz de herramientas de Apache OpenNLP |
| openNLPdata | Modelos de idioma inglés básico y archivos Jar de Apache OpenNLP |
| OutlierDC | Detección de valores atípicos mediante la regresión centil para datos censurados |
| OutlierDM | Detección de valores atípicos para datos replicados de alto rendimiento |
| outliers | Pruebas para valores atípicos |
| pacbpred | Estimación bayesiana de PAC y predicción en modelos aditivos dispersos |
| paralelo | Compatibilidad para el cálculo en paralelo en R |
| partitions | Particiones aditivas de enteros |
| party | Un laboratorio para la creación de particiones recursiva |
| PAWL | Implementación del algoritmo PAWL |
| pbivnorm | CDF normal bivariante vectorizado |
| pcaPP | PCA sólido por búsqueda de proyección |
| permute | Funciones para generar permutaciones restringidas de los datos |
| pls | Regresión de componente principal y cuadrados mínimos parciales |
| plyr | Herramientas para dividir, aplicar y combinar datos |
| png | Leer y escribir imágenes PNG |
| polynom | Una colección de funciones para implementar una clase de manipulaciones polinomiales univariantes |
| PottsUtils | Funciones de utilidad de los modelos de Potts |
| predmixcor | Regla de clasificación basada en los modelos bayesianos mixtos con selección de características corregida por sesgo |
| PresenceAbsence | Evaluación del modelo de presencia-ausencia |
| prodlim | Estimación del límite de producto. Método Kaplan-Meier y Aalen-Johansson para el análisis del historial de eventos censurados (supervivencia) |
| profdpm | Perfil de las mezclas de procesos de Dirichlet |
| profileModel | Herramientas para la generación de perfiles de funciones de inferencia para diversas clases de modelos |
| proto | Programación basada en objetos de prototipo |
| pscl | Laboratorio computacional de ciencia política de la Universidad de Stanford |
| psych | Procedimientos para la investigación sicológica, sicométrica y de personalidad |
| quadprog | Funciones para solucionar problemas de programación cuadrática |
| quantreg | Regresión centil |
| qvcalc | Cuasi-varianzas para efectos de factores en modelos estadísticos |
| R.matlab | Lectura y escritura de archivos MAT en conjunto con la conectividad de R a MATLAB |
| R.methodsS3 | Función de utilidad para definir métodos S3 |
| R.oo | Programación orientada a objetos de R con o sin referencias |
| R.utils | Diversas utilidades de programación |
| R2HTML | Exportación de HTML para objetos de R |
| R2jags | Un paquete para ejecutar JAGS desde R |
| R2OpenBUGS | Ejecución de OpenBUGS desde R |
| R2WinBUGS | Ejecución de WinBUGS y OpenBUGS desde R / S-PLUS |
| ramps | Modelado geoestadístico bayesiano con RAMPS |
| RandomFields | Simulación y análisis de campos aleatorios |
| randomForest | Bosques aleatorios de Breiman y Cutler para clasificación y regresión |
| RArcInfo | Funciones para importar datos desde coberturas binarias de Arc/Info V7.x |
| raster | mapa de bits: modelado y análisis de datos geográficos |
| rbugs | Fusión de R y OpenBugs y más |
| RColorBrewer | Paletas de ColorBrewer |
| Rcpp | Integración sin problemas entre R y C++ |
| RcppArmadillo | Integración de Rccp para la biblioteca de álgebra lineal basada en modelo de Armadillo |
| rcppbugs | Enlace de R para cppbugs |
| RcppEigen | Integración de Rcpp para la biblioteca de álgebra lineal basada en modelo de Eigen |
| RcppExamples | Ejemplos con Rcpp para crear interfaz entre R y C++ |
| RCurl | Interfaz de cliente de red general (HTTP/FTP/...) para R |
| relimp | Contribución relativa de efectos en un modelo de regresión |
| reshape | Volver a dar forma a los datos de manera flexible |
| reshape2 | Volver a dar forma a los datos de manera flexible: un reinicio del paquete de nueva forma |
| rgdal | Enlaces para la biblioteca de abstracción de datos geoespaciales |
| rgeos | Interfaz para Geometry Engine - Open Source (GEOS) |
| rgl | Sistema de dispositivos de visualización 3D (OpenGL) |
| RGraphics | Datos y funciones del libro R Graphics, Second Edition |
| RGtk2 | Enlaces de R para Gtk 2.8.0 y superiores |
| RJaCGH | MCMC de salto reversible para el análisis de matrices de CGH |
| rjags | Modelos gráficos bayesianos con MCMC |
| rJava | Interfaz de R a Java de bajo nivel |
| RJSONIO | Serializar objetos de R a JSON, notación de objetos JavaScript |
| robCompositions | Estimación sólida para datos de composición |
| robustbase | Estadísticas básicas sólidas |
| RODBC | Acceso a base de datos de ODBC |
| rootSolve | Análisis de estado estacionario, análisis de equilibrio y análisis de búsquedas de raíz no lineal de ecuaciones diferenciales ordinarias |
| roxygen | Programación literaria en R |
| roxygen2 | Documentación en origen para R |
| rpart | Árboles de regresión y creación recursiva de particiones |
| rrcov | Estimadores sólidos escalables con punto de ruptura alto |
| rscproxy | statconn: proporciona la interfaz portátil de tipo C a R (StatConnector) |
| RSGHB | Funciones para la estimación bayesiana jerárquica: un enfoque flexible |
| RSNNS | Redes neuronales en R con el simulador de redes neuronales de Stuttgart (SNNS) |
| RTextTools | Clasificación automática de textos a través de aprendizaje supervisado |
| RUnit | Marco de pruebas unitarias de R |
| runjags | Utilidades de interfaz, métodos de cálculo en paralelo y distribuciones adicionales para modelos MCMC en JAGS |
| Runuran | Interfaz de R a los generadores de variantes aleatorias de UNU.RAN |
| rworldmap | Asignación de datos globales, vectores y mapas de bits |
| rworldxtra | Fronteras de países en alta resolución |


###Módulos de R con nombres que comienzan de la S a la Z

| Nombre del paquete | Descripción del paquete |
| ------------ | ------------------- |
| SampleSizeMeans | Cálculos de tamaño de muestra para medias normales |
| SampleSizeProportions | Cálculo de los requisitos de tamaño de muestra cuando se calcula la diferencia entre dos proporciones binomiales |
| sandwich | Estimadores sólidos de la matriz de covarianzas |
| sbgcop | Estimación e imputación bayesiana semiparamétrica de la cópula gaussiana |
| scales | Funciones de escala para gráficos |
| scapeMCMC | Gráficos de diagnóstico de MCMC |
| scatterplot3d | Gráfico de dispersión en 3D |
| sciplot | Funciones de gráficos científicos para diseños factoriales |
| segmented | Relaciones segmentadas en modelos de regresión con estimación de puntos de ruptura/puntos de cambio |
| sem | Modelos de ecuaciones estructurales |
| seriation | Infraestructura para la seriación |
| setRNG | Establecer generador de número aleatorios (normal) y valor de inicialización |
| sgeostat | Un marco orientado a objetos para modelado geoestadístico en S+ |
| shapefiles | Leer y escribir archivos de forma ESRI |
| shiny | Marco de aplicación web para R |
| SimpleTable | Análisis de susceptibilidad e inferencia bayesiana para efectos causales de tablas 2 x 2 y 2 x 2 x K en la presencia de confusión no medida |
| slam | Matrices ligeras dispersas |
| smoothSurv | Regresión de supervivencia con distribución suavizada de errores |
| sna | Herramientas para el análisis de redes sociales |
| snow | Red simple de estaciones de trabajo |
| SnowballC | Lematizadores de Snowball basados en la biblioteca UTF-8 libstemmer de C |
| snowFT | Red simple de estaciones de trabajo con tolerancia a errores |
| sp | clases y métodos para datos espaciales |
| spacetime | clases y métodos para datos espacio-temporales |
| SparseM | Álgebra lineal dispersa |
| spatial | Funciones para el análisis de patrones puntuales y el análisis de Kriging |
| spBayes | Modelado espacio-temporal univariante y multivariante |
| spdep | Dependencia espacial: esquemas de ponderación, estadísticas y modelos |
| spikeslab | Selección de predicción y variable con la regresión Spike-and-Slab |
| splancs | Análisis de patrones puntuales espaciales y espacio-temporales |
| splines | Clases y funciones de curva spline de regresión |
| spTimer | Modelado bayesiano espacio-temporal con R |
| stats | El paquete de estadísticas de R |
| stats4 | Funciones estadísticas con clases S4 |
| stochvol | Inferencia bayesiana eficiente para modelos de volatilidad estocástica (SV) |
| stringr | Se facilita el trabajo con cadenas |
| strucchange | Pruebas, supervisión y fecha de los cambios estructurales |
| stsm | Modelos estructurales de series temporales |
| stsm.class | Clases y métodos para modelos estructurales de series temporales |
| SuppDists | Distribuciones adicionales |
| survival | Análisis de supervivencia |
| svmpath | svmpath: el algoritmo de la ruta de acceso a SVM |
| tau | Utilidades para el análisis de textos |
| tcltk | Interfaz Tcl/Tk |
| tcltk2 | Adiciones de Tcl/Tk |
| TeachingDemos | Demostraciones para enseñanza y aprendizaje |
| tensorA | Aritmética avanzada de tensores con índices con nombres |
| testthat | Código de Testthat. Herramientas para hacer divertidas las pruebas |
| textcat | Categorización de textos basada en N-Grama |
| textir | Regresión inversa para el análisis de textos |
| tfplot | Utilidades de usuario para el período de tiempo |
| tframe | Kernel de codificación del período de tiempo |
| tgp | Modelos bayesianos de procesos gaussianos en árbol |
| TH.data | Archivo de datos de TH |
| timeDate | Rmetrics: objetos cronológicos y de calendario |
| tm | Paquete de minería de texto |
| tools | Herramientas para el desarrollo de paquetes |
| translations | El paquete de traducciones de R |
| tree | Árboles de clasificación y regresión |
| tseries | Finanza computacional y análisis de series temporales |
| tsfa | Análisis de factores de series temporales |
| tsoutliers | Detección automática de valores atípicos en series temporales |
| TSP | Problema del vendedor viajero (TSP) |
| UsingR | Conjuntos de datos para el texto que usa R para la introducción a la estadística |
| utils | El paquete de utilidades de R |
| varSelectIP | Selección objetiva del modelo de Bayes |
| vcd | Visualización de datos categóricos |
| vegan | Paquete ecológico para la comunidad |
| VGAM | Modelo aditivo y modelo lineal generalizados de vectores |
| VIF | Regresión de VIF: un logaritmo de regresión rápida para datos de gran tamaño |
| whisker | {{mustache}} para R, con plantillas sin lógica |
| wordcloud | Nubes de palabras |
| XLConnect | Conector de Excel para R |
| XML | Herramientas para analizar y generar XML dentro de R y S-Plus |
| xtable | Exportar tablas a LaTeX o HTML |
| xts | Series temporales eXtensible |
| yaml | Métodos para convertir datos de R a YAML y viceversa |
| zic | Inferencia bayesiana para modelos de recuento inflados por cero |
| zipfR | Modelos estadísticos para las distribuciones de frecuencia de palabras |
| zoo | Infraestructura de S3 para series temporales regulares e irregulares (observaciones ordenadas por Z) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0204_2016-->