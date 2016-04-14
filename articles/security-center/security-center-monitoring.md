<properties
   pageTitle="Supervisión del estado de seguridad en el Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a comenzar a trabajar con las funcionalidades de supervisión en el Centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="yurid"/>

#Supervisión del estado de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar las funcionalidades de supervisión del Centro de seguridad de Azure para supervisar el cumplimiento de las directivas.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con mayor visibilidad y control de la seguridad de los recursos de Azure. Ofrece administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

##¿Qué es la supervisión del estado de seguridad?
Con frecuencia se piensa que supervisar es observar y esperar que se produzca un evento, para así poder reaccionar ante la situación. La supervisión de seguridad se refiere a contar con una estrategia proactiva que audita los recursos a fin de identificar los sistemas que no cumplen con los estándares o los procedimientos recomendados de la organización.

##Supervisión del estado de seguridad
Después de habilitar las [directivas de seguridad](security-center-policies.md) de los recursos de una suscripción, el Centro de seguridad analizará la seguridad de los recursos para identificar vulnerabilidades potenciales. La información acerca de la configuración de la red está disponible de inmediato; sin embargo, la información acerca de la configuración de las máquinas virtuales, como el estado de las actualizaciones de seguridad y la configuración del sistema operativo, puede tardar una hora, o más, en estar disponible. Puede consultar el estado de seguridad de sus recursos, además de cualquier problema que exista, en las hojas de **Estado de seguridad de los recursos**. También puede ver una lista de esos problemas en las hojas de **Recomendaciones**.

Para más información sobre cómo aplicar las recomendaciones, lea [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md).

El icono **Estado de seguridad de los recursos** permite supervisar el estado de seguridad de los recursos. En el ejemplo siguiente puede ver varios problemas con una gravedad alta y media que requieren atención. Las directivas de seguridad habilitadas afectarán a los tipos de controles que se supervisan.

![Estado de los recursos](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

Si el Centro de seguridad identifica una vulnerabilidad que se debe abordar, como una VM donde faltan actualizaciones de seguridad o una subred sin un [grupo de seguridad de red](virtual-networks-nsg.md), se enumerará aquí.

###Supervisión de máquinas virtuales
Al hacer clic en **Máquinas virtuales** en el icono **Estado de seguridad de los recursos**, se abrirá la hoja **Máquinas virtuales**, donde encontrará más detalles sobre los pasos de incorporación y prevención, así como una lista de las máquinas virtuales que supervisa el Centro de seguridad de Azure, tal como se muestra a continuación.

![Actualización del sistema faltante por VM](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- Pasos para la incorporación
- Recomendaciones sobre máquinas virtuales
- Máquinas virtuales

En cada sección puede seleccionar una opción individual para ver más detalles sobre el paso recomendado para abordar el problema. Las siguientes secciones tratarán estas áreas de forma más detallada.

####Pasos para la incorporación
En esta sección se muestra el número total de máquinas virtuales que se inicializaron para la recopilación de datos y su estado actual. Una vez que se haya inicializado la recopilación de datos en todas las máquinas virtuales, estarán listas para recibir las directivas de seguridad del Centro de seguridad. Al hacer clic en esta entrada, se abre la hoja **Inicialización de recopilación de datos**, donde podrá ver los nombres de las máquinas virtuales y el estado actual de la recopilación de datos en la columna **ESTADO DE INICIALIZACIÓN**, como se muestra a continuación:

![Estado de la inicialización](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Recomendaciones sobre máquinas virtuales
Esta sección contiene un conjunto de recomendaciones para cada máquina virtual supervisada por el centro de seguridad de Azure. La primera columna enumera la recomendación, la segunda el número total de máquinas virtuales afectadas por dicha recomendación y la tercera muestra la gravedad del problema, tal como se ilustra a continuación.

![Recomendaciones de máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Cada recomendación tiene un conjunto de acciones que se pueden realizar una vez que haga clic en ella. Por ejemplo, si hace clic en **Actualizaciones del sistema que faltan**, se abrirá la hoja **Actualizaciones del sistema que faltan**. En dicha hoja se enumeran las máquinas virtuales a las que faltan revisiones y la gravedad de la actualización que falta, como se muestra a continuación.

![Actualizaciones del sistema que faltan](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

La hoja **Actualizaciones del sistema que faltan** mostrará una tabla con la siguiente información:

- **MÁQUINA VIRTUAL**: el nombre de la máquina virtual en la que faltan actualizaciones.
- **ACTUALIZACIONES DEL SISTEMA**: el número actualizaciones del sistema que faltan.
- **HORA DEL ÚLTIMO EXAMEN**: la hora en que el Centro de seguridad realizó el último examen de la máquina virtual en busca de actualizaciones.
- **ESTADO**: el estado actual de la recomendación: 
	- **Abierta**: la recomendación aún no se ha abordado.
	- **En curso**: la recomendación se aplica actualmente a los recursos; no se requiere ninguna acción de su parte.
	- **Resuelta**: la recomendación se completó (cuando el problema se ha resuelto, la entrada aparece atenuada).
- **GRAVEDAD**: describe la gravedad de una recomendación concreta: 
	- **Alta**: existe una vulnerabilidad en un recurso importante (aplicación, VM, grupo de seguridad de red) y requiere atención.
	- **Media**: para completar un proceso o eliminar una vulnerabilidad se requieren pasos adicionales o no críticos.
	- **Baja**: es preciso abordar una vulnerabilidad, pero esta no requiere una atención inmediata. (De manera predeterminada no se muestran las recomendaciones bajas, pero si desea verlas, puede filtrar por ellas).

Para ver los detalles de las recomendaciones, haga clic en el nombre de la VM. Se abre una hoja nueva de la VM con la lista de las actualizaciones, tal como se muestra a continuación.

![Actualizaciones del sistema que faltan por máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Las recomendaciones de seguridad son las mismas que aparecen en la hoja Recomendaciones. Vea el artículo [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md) para más información sobre cómo solucionar las recomendaciones. Esto no se aplica solo a las VM, sino que también a todos los recursos disponibles en el icono Estado de los recursos.

####Sección Máquinas virtuales
La sección de máquinas virtuales ofrece una visión general de todas las máquinas virtuales y recomendaciones. Cada columna representa un conjunto de recomendaciones, tal como se muestra a continuación:

![Máquinas virtuales](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

El icono que aparece en cada recomendación le ayuda a identificar rápidamente tanto las máquinas virtuales que necesitan atención como el tipo de recomendación que necesitan.

En el ejemplo anterior, una VM tiene una recomendación crítica acerca de los programas antimalware. Para más información acerca de la VM, haga clic en ella. Se abrirá una nueva hoja que representa la VM, tal como se muestra a continuación.

![Detalles de seguridad de la máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Esta hoja tiene los detalles de seguridad de la máquina virtual. En la parte inferior de la hoja puede ver la acción recomendada y la gravedad de cada problema.

###Supervisión de redes virtuales
La sección de estado de prevención de las redes enumera las redes virtuales que supervisa el Centro de seguridad. Al hacer clic en **Redes** en el icono **Estado de seguridad de los recursos**, se abrirá la hoja **Redes**, donde encontrará con más detalles, tal como se muestra a continuación:

![Redes](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

Una vez que abra esta hoja, verá dos secciones: - Recomendaciones de redes - Redes.
 
En cada sección puede seleccionar una opción individual para información acerca de la recomendación. Las siguientes secciones tratarán estas áreas de forma más detallada.

####Recomendaciones de redes

De manera similar a la información del estado de los recursos de las máquinas virtuales, esta hoja proporciona una lista resumida de los problemas en la parte superior de la hoja y una lista de las redes supervisadas en la parte inferior.

![Extremo](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

La sección de desglose del estado de las redes enumera los potenciales problemas de seguridad y ofrece recomendaciones. Entre los posibles problemas se pueden incluir:

- [ACL en puntos de conexión](virtual-machines-set-up-endpoints.md) no habilitadas
- [Grupos de seguridad de red](virtual-networks-nsg.md) no habilitados
- Se enumeran las subredes en buen estado y el acceso en NSG no restringido. 
 
Al hacer clic en una de las recomendaciones, se abrirá una nueva hoja con más detalles acerca de ella, como se muestra en el ejemplo siguiente.

![Restricción del punto de conexión](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

En este ejemplo, la hoja **Restringir acceso a través de un punto de conexión externo público** tiene una lista de grupos de seguridad de red (NSG) que forman parte de esta alerta, la subred y la red con las que la NSG está asociada, el estado actual de la recomendación y la gravedad del problema. Si hace clic en el grupo de seguridad de red, se abrirá otra hoja, como se muestra a continuación.

Esta hoja contiene la información y ubicación del grupo de seguridad de red. También contiene la lista de reglas de entrada activadas actualmente. La parte inferior de esta hoja tiene la máquina virtual que está asociada a este grupo de seguridad de red. Si desea habilitar las reglas de entrada para bloquear un puerto no deseado que esté actualmente abierto o cambiar el origen de la regla de entrada actual, haga clic en el botón **Editar regla de entrada** de la parte superior de la hoja.

####Sección Redes

En la sección **Redes** hay una vista jerárquica del grupo de recursos, la subred y la interfaz de red asociada a la máquina virtual, como se muestra a continuación.

![Árbol de red](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

En esta sección divide [las máquinas virtuales basadas en el Administrador de recursos de las máquinas virtuales clásicas](resource-manager-deployment-model.md). Esto le ayudará a identificar rápidamente si las funcionalidades de red de Administración de servicios de Azure o Administración de recursos de Azure están disponibles para la máquina virtual. Si decide obtener acceso a las propiedades de una tarjeta de la interfaz de red desde esta ubicación, tendrá que expandir la subred y hacer clic en el nombre de la máquina virtual. Si realiza esta acción para una máquina virtual basada en el Administrador de recursos, aparecerá una nueva hoja similar a la siguiente.

![Árbol de red](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

Esta hoja tiene un resumen de la tarjeta de la interfaz de red y las recomendaciones actuales para ella. Si la VM que seleccionó es una VM clásica, aparecerá una nueva hoja con diferentes opciones, como se muestra a continuación.

![ACL](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

En esta hoja, puede realizar cambios en los puertos públicos y privados, así como crear una ACL para la VM.

###Supervisión de recursos de SQL
Al hacer clic en **SQL** en el icono **Estado de seguridad de los recursos**, se abrirá la hoja SQL con las recomendaciones para los problemas, como que no están habilitados una auditoría o un cifrado de datos transparente. También tiene las recomendaciones sobre el estado general de la base de datos.

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Puede hacer clic en cualquiera de estas recomendaciones para obtener más detalles sobre las acciones que deben llevarse a cabo para solucionar el problema. El ejemplo siguiente muestra la expansión de la recomendación **Auditoría de base de datos no habilitada**.

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

La hoja **Habilitar auditoría en bases de datos SQL** hoja tiene la siguiente información:

- Una lista de bases de datos SQL.
- El servidor en el que se encuentran.
- Información sobre si esta configuración se heredó del servidor o si es única en la base de datos.
- El estado actual.
- La gravedad del problema.

Al hacer clic en la base de datos para abordar esta recomendación, se abrirá la hoja **Auditoría y detección de amenazas**, como se muestra a continuación:

![Estado de los recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Para habilitar la auditoría, seleccione **Activar** en la opción **Auditoría** y haga clic en **Guardar**.

###Supervisión de aplicaciones
Si la carga de trabajo de Azure tiene aplicaciones en las [máquinas virtuales del Administrador de recursos](resource-manager-deployment-model.md) con puertos web expuestos (puertos TCP 80 y 443), el Centro de seguridad puede supervisarlos para identificar problemas de seguridad potenciales y recomendar pasos para su corrección. Al hacer clic en el icono **Aplicaciones**, se abrirá la hoja **Aplicaciones** con una serie de recomendaciones en la sección de pasos de prevención. También mostrará el desglose de la aplicación por host o IP virtual, tal como aparece a continuación.

![Estado de la seguridad de las aplicaciones](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Al igual que en las restantes recomendaciones, puede hacer clic en ella para ver más información acerca del problema y cómo corregirlo. El ejemplo que se muestra en la ilustración siguiente es una aplicación que se identificó como aplicación web no segura. Cuando selecciona la aplicación que se consideró como no segura, se abrirá otra hoja con la siguiente opción disponible:

![Aplicaciones](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

La hoja **Aplicaciones web no seguras** incluirá una lista de todas las máquinas virtuales que contienen aplicaciones que no se consideran seguras. La lista muestra el nombre de la VM, así como el estado actual y la gravedad del problema. Si hace clic en esta aplicación web, se abrirá la hoja **Agregar un Firewall de aplicaciones web** con opciones para instalar un WAF (Firewall de aplicaciones web) de terceros, como se muestra a continuación.

![Adición de WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Pasos siguientes
En este documento, aprendió a usar las funcionalidades de supervisión en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información sobre cómo configurar los ajustes de seguridad en el Centro de seguridad de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0211_2016-->