<properties 
	pageTitle="Escalado automático y entorno del Servicio de aplicaciones" 
	description="Escalado automático y entorno del Servicio de aplicaciones" 
	services="app-service"
	documentationCenter=""
	authors="btardif" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="byvinyal"
/>
	
#Escalado automático y entorno del Servicio de aplicaciones

##Introducción
Los **entornos del Servicio de aplicaciones** admiten el escalado automático. Esto se consigue permitiendo realizar el escalado automático de grupos de trabajo individuales en función de métricas o programación.
 
![][intro]
 
El escalado automático permite optimizar el uso de recursos gracias al crecimiento y reducción automáticos de un **entorno del Servicio de aplicaciones** para ajustarse a su presupuesto o a su perfil de carga.

##Configuración del escalado automático en el grupo de trabajo 
Puede acceder a la funcionalidad de escalado automática desde la ficha Configuración del **grupo de trabajo**.
 
![][settings-scale]

A partir de ahí la interfaz debe resultar bastante familiar ya que se trata de la misma experiencia que en el escalado de un **plan del Servicio de aplicaciones**. Podrá especificar manualmente un valor de escala
 
![][scale-manual]
 
O bien podrá configurar un perfil de escalado automático:
 
![][scale-profile]
 
Los perfiles de escalado automático resultan útiles para establecer límites en la escala. De este modo puede disponer tanto de una experiencia de rendimiento coherente, mediante el establecimiento de un valor de escala de límite inferior (1), como de un techo de gasto predecible, mediante el establecimiento de un límite superior (2).
 
![][scale-profile2]
 
Una vez definido un perfil, se pueden agregar reglas de escalado automático basadas en métricas para escalar o reducir verticalmente el número de instancias en el **grupo de trabajo** que se encuentran dentro de los límites definidos por el perfil.

![][scale-rule]

 En la definición de las reglas de escalado automático se pueden usar las métricas del **grupo de trabajo** o de **front-end**. Son las mismas métricas que puede supervisar en los gráficos de la hoja de recursos o para las que puede configurar alertas.
 
##Ejemplo de escalado automático
La mejor manera de ilustrar el escalado automático de un **entorno del Servicio de aplicaciones** es a través de un escenario. En este artículo veremos todas las consideraciones que se deben tener en cuenta al configurar el escalado automático y todas las interacciones que entran en juego cuando aplicamos el escalado automático en **entornos del Servicio de aplicaciones** que se hospedan en un ASE.

###Introducción al escenario
Frank es administrador de sistemas de una empresa y ha migrado parte de las cargas de trabajo que administra a un **entorno del Servicio de aplicaciones**.

El **entorno del Servicio de aplicaciones** está configurado para el escalado manual de la manera siguiente: * Grupo de servidores front-end: 3 * Grupo de trabajo 1: 10 * Grupo de trabajo 2: 5 * Grupo de trabajo 3: 5

El **grupo de trabajo 1** se usa para las cargas de trabajo de producción, en tanto que el **grupo de trabajo 2** y el **grupo de trabajo 3** se usan para las cargas de trabajo de control de calidad y desarrollo.

Los **planes del Servicio de aplicaciones** usados para control de calidad y desarrollo están configurados para el **escalado manual** pero el **plan del Servicio de aplicaciones** de producción está configurado para el **escalado automático**, para adaptarse a las variaciones en la carga y el tráfico.

Frank está muy familiarizado con la aplicación y sabe que las horas pico de carga tienen lugar entre las 09:00 y las 18:00, puesto que se trata de una **aplicación de línea de negocio** que usan los empleados mientras están en la oficina. El uso cae después, una vez que los usuarios han terminado la jornada. Aún queda todavía cierta carga, ya que los usuarios pueden acceder de forma remota con sus dispositivos móviles o equipos domésticos. El **plan del servicio de Aplicaciones** de producción ya está configurado para el **escalado automático** en función del uso de CPU con las reglas siguientes:

![][asp-scale]

|	**Perfil de escalado automático - Días laborables – Plan del servicio de aplicaciones** |	**Perfil de escalado automático - Fines de semana – Plan del servicio de aplicaciones** |
|	----------------------------------------------------	|	----------------------------------------------------	|
|	**Nombre:** Perfil de día laborable |	**Nombre:** Perfil de fin de semana |
|	**Escalar por:** Reglas de rendimiento y programación |	**Escalar por:** Reglas de rendimiento y programación |
|	**Perfil:** Días laborables |	**Perfil:** Fin de semana |
|	**Tipo:** periodicidad |	**Tipo:** periodicidad |
|	**Rango objetivo:** 5 a 20 instancias |	**Rango objetivo:** 3 a 10 instancias |
|	**Días:** lunes, martes, miércoles, jueves, viernes |	**Días:** sábado, domingo |
|	**Hora de inicio:** 9:00 A.M. |	**Hora de inicio:** 9:00 A.M. |
|	**Zona horaria:** UTC – 08 |	**Zona horaria:** UTC – 08 |
| | |
|	**Regla de escalado automático (escalar verticalmente)** |	**Regla de escalado automático (escalar verticalmente)** |
|	**Recurso:** Producción (entorno del Servicio de aplicaciones) |	**Recurso:** Producción (entorno del Servicio de aplicaciones) |
|	**Métrica:** % de CPU |	**Métrica:** % de CPU |
|	**Operación:** Mayor que 60% |	**Operación:** Mayor que 80% |
|	**Duración:** 5 minutos |	**Duración:** 10 minutos |
|	**Agregación de tiempo:** Media |	**Agregación de tiempo:** Media |
|	**Acción:** Aumentar recuento en 2 |	**Acción:** Aumentar recuento en 1 |
|	**Tiempo de finalización (minutos):** 15 |	**Tiempo de finalización (minutos):** 20 |
| | |
|	**Regla de escalado automático (reducir verticalmente)** |	**Regla de escalado automático (reducir verticalmente)** |
|	**Recurso:** Producción (entorno del Servicio de aplicaciones) |	**Recurso:** Producción (entorno del Servicio de aplicaciones) |
|	**Métrica:** % de CPU |	**Métrica:** % de CPU |
|	**Operación:** Menor que 30% |	**Operación:** Menor que 20% |
|	**Duración:** 10 minutos |	**Duración:** 15 minutos |
|	**Agregación de tiempo:** Media |	**Agregación de tiempo:** Media |
|	**Acción:** Reducir recuento en 1 |	**Acción:** Reducir recuento en 1 |
|	**Tiempo de finalización (minutos):** 20 |	**Tiempo de finalización (minutos):** 10 |

###Tasa de inflación del plan del Servicio de aplicaciones
Los **planes del Servicio de aplicaciones** configurados para el escalado automático lo realizarán a una tasa máxima por hora. Esta tasa se puede calcular en función de los valores indicados en la regla de escalado automático.

Comprender y calcular la **tasa de inflación del plan del Servicio de aplicaciones** es un objetivo importante para el escalado automático del **grupo de trabajo** del **entorno del Servicio de aplicaciones**, ya que los cambios en el escalado de un **grupo de trabajo** no son instantáneos y tardan algún tiempo en aplicarse.

La tasa de inflación del **plan del Servicio de aplicaciones** se calcula del siguiente modo:

![][ASP-Inflation]

En función de la regla *Escalado automático - Escalar verticalmente* del perfil *Días laborables* del **plan del Servicio de aplicaciones** de producción, sería:

![][Equation1]

En el caso de la regla *Escalado automático - Escalar verticalmente* del perfil *Fines de semana* del **plan del Servicio de aplicaciones** de producción, la fórmula daría como resultado:

![][Equation2]

Este valor también se puede calcular para operaciones de reducción vertical:

En función de la regla *Escalado automático - Reducir verticalmente* del perfil *Días laborables* del **plan del Servicio de aplicaciones** de producción, sería:

![][Equation3]

En el caso de la regla *Escalado automático - Reducir verticalmente* del perfil *Fines de semana* del **plan del Servicio de aplicaciones** de producción, la fórmula daría como resultado:

![][Equation4]

Esto significa que el **plan del Servicio de aplicaciones** de producción puede crecer a una tasa máxima de **8** instancias por hora durante la semana y de **4** instancias por hora durante los fines de semana. Además, puede liberar las instancias a una tasa máxima de **4** instancias por hora durante la semana y **6** instancias por hora durante los fines de semana.

Si se hospedan varios **Planes del Servicio de aplicaciones** en un **grupo de trabajo**, debe calcularse la **tasa de inflación total**, que se puede expresar como la *suma* de la tasa de inflación de todos los **Planes del Servicio de aplicaciones** que se hospedan en dicho **grupo de trabajo**.

![][ASP-Total-Inflation]

###Uso de la tasa de inflación del plan del Servicio de aplicaciones para definir reglas de escalado automático del grupo de trabajo
Los **grupos de trabajo** que hospedan **planes del Servicio de aplicaciones** configurados en el escalado automático deberán asignar un búfer de capacidad para permitir que las operaciones de escalado automático amplíen o reduzcan el **plan del Servicio de aplicaciones** según sea necesario. El búfer mínimo será la **tasa de inflación total del plan del Servicio de aplicaciones** calculada.

Puesto que las operaciones de escalado del **entorno del Servicio de aplicaciones** tardan algún tiempo en aplicarse, deben tenerse en cuenta los cambios de demanda adicionales que se pueden producir mientras están en curso una operación de escalado. Para ello, se recomienda usar la **tasa de inflación total del plan del Servicio de aplicaciones** calculada como el número mínimo de instancias que se agregan para cada operación de escalado automático.

Con esta información, Frank puede definir el siguiente perfil y las siguientes reglas de escalado automático

![][Worker-Pool-Scale]

|	**Perfil de escalado automático - Días laborables** |	**Perfil de escalado automático - Fines de semana** |
|	----------------------------------------------------	|	--------------------------------------------	|
|	**Nombre:** Perfil de día laborable |	**Nombre:** Perfil de fin de semana |
|	**Escalar por:** Reglas de rendimiento y programación |	**Escalar por:** Reglas de rendimiento y programación |
|	**Perfil:** Días laborables |	**Perfil:** Fin de semana |
|	**Tipo:** periodicidad |	**Tipo:** periodicidad |
|	**Rango objetivo:** 13 a 25 instancias |	**Rango objetivo:** 6 a 15 instancias |
|	**Días:** lunes, martes, miércoles, jueves, viernes |	**Días:** sábado, domingo |
|	**Hora de inicio:** 7:00 A.M. |	**Hora de inicio:** 9:00 A.M. |
|	**Zona horaria:** UTC – 08 |	**Zona horaria:** UTC – 08 |
| | |
|	**Regla de escalado automático (escalar verticalmente)** |	**Regla de escalado automático (escalar verticalmente)** |
|	**Recurso:** Grupo de trabajo 1 |	**Recurso:** Grupo de trabajo 1 |
|	**Métrica:** WorkersAvailable |	**Métrica:** WorkersAvailable |
|	**Operación:** Menor que 8 |	**Operación:** Menor que 3 |
|	**Duración:** 20 minutos |	**Duración:** 30 minutos |
|	**Agregación de tiempo:** Media |	**Agregación de tiempo:** Media |
|	**Acción:** Aumentar recuento en 8 |	**Acción:** Aumentar recuento en 3 |
|	**Tiempo de finalización (minutos):** 90 |	**Tiempo de finalización (minutos):** 90 |
| | |
|	**Regla de escalado automático (reducir verticalmente)** |	**Regla de escalado automático (reducir verticalmente)** |
|	**Recurso:** Grupo de trabajo 1 |	**Recurso:** Grupo de trabajo 1 |
|	**Métrica:** WorkersAvailable |	**Métrica:** WorkersAvailable |
|	**Operación:** Mayor que 8 |	**Operación:** Menor que 3 |
|	**Duración:** 20 minutos |	**Duración:** 15 minutos |
|	**Agregación de tiempo:** Media |	**Agregación de tiempo:** Media |
|	**Acción:** Reducir recuento en 2 |	**Acción:** Reducir recuento en 3 |
|	**Tiempo de finalización (minutos):** 90 |	**Tiempo de finalización (minutos):** 90 |

El rango objetivo definido en el perfil se calcula sumando el número mínimo de instancias definido en el perfil para el **plan del Servicio de aplicaciones** + búfer.

El rango máximo debe ser la suma de todos los rangos máximos de todos los **planes del Servicio de aplicaciones** alojados en el **grupo de trabajo**.

El recuento de aumento de las reglas de escalado vertical se debe configurar al menos en 1X de la **tasa de inflación del Plan del Servicio de aplicaciones** para el escalado vertical.

El recuento de reducción se puede ajustar en un valor situado entre 1/2X y 1X de la **tasa de inflación del plan del Servicio de aplicaciones** para la reducción vertical.

###Escalado automático para el grupo de servidores front-end
Las reglas de escalado automático para **servidores front-end** son más sencillas que para los **grupos de trabajo**; el principal aspecto que se debe contemplar es asegurarse de que la duración de la medida y los temporizadores de enfriamiento tienen en cuenta el hecho de que las operaciones de escalado en un **plan del Servicio de aplicaciones** no son instantáneas.

En este escenario, Frank sabe que la tasa de errores aumenta una vez que los servidores front-end alcanzan el 80% de uso de CPU; para evitarlo, configura la regla de escalado automático para que aumente las instancias como sigue:
 
![][Front-End-Scale]
  
|	**Perfil de escalado automático - Servidores front-end** |
|	--------------------------------------------	|
|	**Nombre:** Escalado automático - Servidores front-end |
|	**Escalar por:** Reglas de rendimiento y programación |
|	**Perfil:** Todos los días |
|	**Tipo:** periodicidad |
|	**Rango objetivo:** 3 a 10 instancias |
|	**Días:** Todos los días |
|	**Hora de inicio:** 9:00 A.M. |
|	**Zona horaria:** UTC – 08 |
| |
|	**Regla de escalado automático (escalar verticalmente)** |
|	**Recurso:** Grupo de servidores front-end |
|	**Métrica:** % de CPU |
|	**Operación:** Mayor que 60% |
|	**Duración:** 20 minutos |
|	**Agregación de tiempo:** Media |
|	**Acción:** Aumentar recuento en 3 |
|	**Tiempo de finalización (minutos):** 90 |
| |
|	**Regla de escalado automático (reducir verticalmente)** |
|	**Recurso:** Grupo de trabajo 1 |
|	**Métrica:** % de CPU |
|	**Operación:** Menor que 30% |
|	**Duración:** 20 minutos |
|	**Agregación de tiempo:** Media |
|	**Acción:** Reducir recuento en 3 |
|	**Tiempo de finalización (minutos):** 90 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

<!---HONumber=AcomDC_0107_2016-->