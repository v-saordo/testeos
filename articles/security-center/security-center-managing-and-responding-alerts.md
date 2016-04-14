<properties
   pageTitle="Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar y responder a las alertas de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/11/2016"
   ms.author="yurid"/>
 
# Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar las alertas de seguridad y responder a ellas.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## ¿Qué es el Centro de seguridad de Azure?
 El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## ¿Qué son las alertas de seguridad?
El Centro de seguridad recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y los firewalls y programas antimalware para detectar amenazas reales y reducir los falsos positivos. En el Centro de seguridad, se muestra una lista de alertas de seguridad prioritarias, incluidas las procedentes de soluciones integradas de asociados, junto con la información que necesita para investigar rápidamente y recomendaciones para corregir un ataque.
 
Los investigadores en seguridad de Microsoft están analizando constantemente las amenazas que surgen en todo el mundo, incluidos los nuevos patrones y tendencias de ataque vistos en sus productos para consumidores y empresas, y servicios en línea. Como consecuencia, el Centro de seguridad puede actualizar sus algoritmos de detección a medida que se descubren nuevas vulnerabilidades para ayudar a los clientes a mantenerse al ritmo de las amenazas cambiantes. Algunos ejemplos de los tipos de amenazas que puede detectar el Centro de seguridad son:

- **Detección por fuerza bruta en los datos de red**: usa los modelos de aprendizaje automático que comprenden los patrones típicos del tráfico de red para las aplicaciones y permite una detección más eficaz de los intentos de acceso llevados a cabo por actores malintencionados en lugar de por usuarios legítimos.
- **Detección por fuerza bruta en datos de puntos de conexión**: se basa en el análisis de registros de la máquina; esto permite diferenciar entre intentos incorrectos y correctos.
- **Máquinas virtuales comunicándose con direcciones IP malintencionadas**: compara el tráfico de red con la inteligencia sobre amenazas globales de Microsoft y detecta las máquinas que están en peligro y comunicándose con servidores de comando y control (C&C) y viceversa.
- **Máquinas virtuales en peligro**: en función del análisis del comportamiento de registros de máquina y la correlación con otras señales, identifica los eventos anómalos que probablemente sean resultado de la existencia de máquinas vulnerables y en peligro.

## Administración de alertas de seguridad 

Puede revisar las alertas actuales en el icono **Alertas de seguridad**. Siga los pasos siguientes para ver más detalles sobre cada alerta:

1. En el panel del Centro de seguridad, verá el icono **Alertas de seguridad**.

    ![El icono Alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  Haga clic en el icono para abrir la hoja **Alertas de seguridad** que contiene información más detallada acerca de las alertas, como se muestra a continuación.

    ![La hoja Alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

En la parte inferior de esta hoja aparecen los detalles de cada alerta. Para ordenar, haga clic en la columna que desea ordenar. A continuación se muestra la definición de cada columna:

- **Alerta**: una breve explicación de la alerta.
- **Recuento**: una lista de todas las alertas de este tipo específico que se han detectado en un día concreto.
- **Detectado por**: el servicio responsable de desencadenar la alerta.
- **Fecha**: la fecha en que se ha producido el evento.
- **Estado**: el estado actual de esa alerta. Existen tres tipos de estado:
    - **Activa**: se ha detectado la alerta de seguridad.
    - **Descartada**: el usuario ha descartado la alerta de seguridad. Este estado suele utilizarse para alertas que se han investigado pero que no se han mitigado, o se ha observado que no plantean un ataque real.

- **Gravedad**: el nivel de gravedad, que puede ser alto, medio o bajo.

Puede filtrar alertas en función de la fecha, el estado y la gravedad. Puede resultar útil filtrar las alertas en aquellos escenarios en que necesite restringir el ámbito de las alertas de seguridad que se muestran. Por ejemplo, podría comprobar las alertas de seguridad que se produjeron en las 24 horas anteriores, ya que se está investigando una posible brecha en el sistema.

1. Haga clic en **Filtro** en la hoja **Alertas de seguridad**. Se abre la hoja **Filtro**, donde podrá seleccionar los valores de fecha, estado y gravedad que desee ver.

	![Filtrar alertas en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2. 	Después de investigar una alerta de seguridad, es posible que descubra que es un falso positivo para su entorno o que indica un comportamiento normal de un recurso determinado. En cualquier caso, si determina que una alerta de seguridad no es aplicable, puede descartarla y después filtrarla de la vista. Hay dos formas de descartar una alerta de seguridad. Haga clic con el botón derecho en una alerta y seleccione **Descartar**, o bien mantenga el mouse sobre un elemento, haga clic en los tres puntos que aparecen a la derecha y seleccione **Descartar**. Para ver las alertas de seguridad descartadas, haga clic en **Filtro** y seleccione **Descartadas**.

	![Filtrar alertas en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Responder a alertas de seguridad
Seleccione una alerta de seguridad para ver más información sobre el evento o los eventos que la desencadenaron y, si existen, los pasos que debe seguir para corregir un ataque. Las alertas de seguridad se agrupan según el tipo y la fecha. Haga clic en una alerta de seguridad y se abrirá una hoja que contiene una lista de las alertas agrupadas.

![Responder a alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

En este caso, las alertas desencadenadas hacen referencia a las actividades sospechosas del Protocolo de escritorio remoto (RDP). En la primera columna se muestran los recursos atacados; en la segunda, la hora a la que se detectó el ataque; en la tercera, el estado de la alerta; y en la cuarta, la gravedad del ataque. Después de revisar esta información, haga clic en el recurso atacado. Se abrirá una nueva hoja con más sugerencias sobre qué hacer a continuación, tal como se muestra en el ejemplo siguiente.

![Sugerencias sobre qué hacer con las alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8.png)

> [AZURE.NOTE] La corrección sugerida por el Centro de seguridad variará según la alerta de seguridad. En algunos casos, tendrá que utilizar otras capacidades de Azure para implementar la corrección recomendada. Por ejemplo, la corrección para este ataque consiste en colocar la dirección IP que lo está generando en la lista negra mediante una [ACL de red](../virtual-network/virtual-networks-acl.md) o una regla de [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md).


## Pasos siguientes
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0218_2016-->