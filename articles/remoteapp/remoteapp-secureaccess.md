
<properties 
    pageTitle="Protección del acceso a Azure RemoteApp, y mucho más | Microsoft Azure"
	description="Obtenga información sobre cómo proteger el acceso a Azure RemoteApp con acceso condicional en Azure Active Directory"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/12/2016" 
    ms.author="elizapo" />

# Protección del acceso a Azure RemoteApp, y mucho más

En este artículo ofrecemos información sobre cómo un administrador puede configurar un canal de acceso seguro que comienza en el usuario final, a través de Azure RemoteApp y que finaliza con un recurso seguro, como una base de datos SQL o un back-end de otra aplicación. El objetivo es asegurarse de que solo los usuarios autorizados que cumplen las condiciones deseadas pueden tener acceso a las aplicaciones remotas y que solo se pueda acceder al back-end seguro desde el entorno de Azure RemoteApp controlado y no desde otras ubicaciones.

Hay tres áreas principales que debe tener en cuenta el administrador:

![Consideraciones de acceso condicional de Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Siga leyendo para obtener más información y respuestas a estas preguntas.

## ¿Quién puede acceder a la colección?
El administrador elige a los usuarios que pueden tener acceso a las aplicaciones remotas en la colección. Puede usar cuentas profesionales o educativas (anteriormente denominadas "cuentas organizativas") de Azure Active Directory (Azure AD) o cuentas de Microsoft (por ejemplo, @outlook.com). La mayoría de los escenarios de empresa usan cuentas de Azure AD; le permiten usar características de acceso condicional, que se describen más adelante, y también son la única opción para las colecciones unidas a un dominio. El resto del artículo se supone que usa cuentas de Azure AD con Azure RemoteApp.

**¿Qué hemos logrado?**

El uso de las cuentas de Azure AD para controlar el acceso a Azure RemoteApp ofrece dos ventajas:

1.	Siempre sabemos quién tiene acceso a las aplicaciones que hemos publicado y accedemos a cualquier back-end al que estén conectados las aplicaciones.
2.	Controlamos el Azure AD subyacente para que podamos crear y eliminar cuentas de usuario, establecer directivas de contraseña o utilizar la autenticación multifactor, entre otras posibilidades. 

## ¿Cómo se accede a la colección? ¿Desde dónde?
Normalmente los administradores quieren definir directivas para el acceso a un entorno de Internet de acceso público, como Azure RemoteApp. Por ejemplo, quieren asegurarse de que los usuarios que acceden al entorno desde fuera de la red corporativa tengan que usar Multi-Factor Authentication (MFA) para obtener acceso; o quizás deben bloquearse por completo.

Los administradores de Azure RemoteApp pueden utilizar la funcionalidad disponible a través de Azure AD Premium para establecer directivas de acceso condicional para su entorno de Azure RemoteApp. También pueden usar informes enriquecidos y características de alerta para supervisar cómo se accede al entorno.

### Configuración del acceso condicional para Azure RemoteApp
Vamos a describir un escenario de ejemplo: el administrador de Azure RemoteApp quiere bloquear el acceso al entorno cuando los usuarios están fuera de la red corporativa.

>[AZURE.NOTE]Se asume que ha actualizado Azure AD al nivel Premium y que ha creado al menos una colección de Azure RemoteApp.

1.	En el Portal de Azure, haga clic la pestaña **Active Directory**. Después, haga clic en el directorio que quiere configurar.

	Recuerde: el acceso condicional es una propiedad de su directorio y no de Azure RemoteApp, por lo que toda la configuración se realiza en el nivel del directorio. Esto también significa que debe ser administrador de directorio para realizar estos cambios.

2.	Haga clic en **Aplicaciones** y, después, haga clic en **Microsoft Azure RemoteApp** para configurar el acceso condicional. Tenga en cuenta que puede configurar el acceso condicional para cada aplicación de "software como servicio" en su directorio por separado. ![Configuración de acceso condicional para Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.	En la pestaña **Configurar**, establezca **Habilitar reglas de acceso** en ON. ![Habilitación de reglas de acceso para Azure RemoteApp](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.	Ahora puede configurar varias reglas y elegir a quién aplicarlas:

	1. Elija **Bloquear acceso cuando no está en el trabajo** para evitar completamente que los usuarios accedan a Azure RemoteApp fuera del entorno de red que especifique.
	2. Haga clic en la opción siguiente para definir los intervalos de las direcciones IP que constituyen su "red de confianza". Se rechazarán las direcciones fuera de este intervalo.

5.	Pruebe la configuración mediante el inicio del cliente de Azure RemoteApp desde una dirección IP fuera del intervalo especificado. Después de haber iniciado sesión con sus credenciales de Azure AD, verá un mensaje similar al siguiente:

![Acceso denegado a Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### Futuras características del acceso condicional 
El equipo de Azure Active Directory está trabajando en nuevas funcionalidades del acceso condicional. Los administradores podrán crear nuevos tipos de reglas a partir de las reglas basadas en la ubicación de red. Pronto estará disponible una versión preliminar pública de la nueva funcionalidad.

### Supervisión del acceso a Azure RemoteApp
Una excelente característica para usar con el acceso condicional es la funcionalidad de informes de Azure Active Directory Premium. Puede utilizar informes para supervisar quién accede a su entorno y detectar cualquier actividad sospechosa.

Por ejemplo, puede ver los nombres de los usuarios que tienen acceso a Azure RemoteApp, cuántas veces accedieron y cuándo.

1.	En el Portal de Azure, haga clic en **Active Directory** y después haga clic en el directorio.

2.	Haga clic en la pestaña **Informes**.

3.	En la lista de informes, seleccione **Uso de aplicaciones** en **Aplicaciones integradas**.

	Podrá ver algunas estadísticas agregadas para Azure RemoteApp. ![Estadísticas de acceso de Azure RemoteApp agregadas](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.	Haga clic en la aplicación para mostrar información sobre los usuarios que acceden a Azure RemoteApp. ![Uso de estadísticas de acceso para Azure RemoteApp](./media/remoteapp-secureaccess/ra-userstats.png)
 
### Resumen
Con Azure Active Directory Premium puede configurar reglas de acceso a Azure RemoteApp (y a otras aplicaciones de software como servicio disponibles a través de Azure AD). Las reglas están limitadas actualmente a directivas basadas en ubicación de red, pero en el futuro se ampliarán a otros aspectos de la administración empresarial.

Azure AD Premium también ofrece funcionalidades de informes y de supervisión que extienden aún más el control que ejerce la administración sobre su entorno de Azure RemoteApp.

## ¿Cómo puedo asegurarme de que mis recursos seguros solo son accesible desde mi entorno de Azure RemoteApp?
En las secciones anteriores de este artículo, nos centramos en proteger el acceso al entorno de Azure RemoteApp. Lo hemos conseguido mediante la elección de los usuarios que tienen permiso de acceso y mediante la configuración de reglas de acceso para controlar mejor cómo pueden usar el servicio.

Un escenario común para las implementaciones de Azure RemoteApp es que las aplicaciones remotas necesitan comunicarse con un recurso de back-end, por ejemplo una Base de datos SQL. Este recurso se hospeda de forma local (por ejemplo, en una red corporativa) o en la nube (por ejemplo, en Azure IaaS). A menudo, los administradores desean asegurarse de que el recurso de back-end solo sea accesible para las aplicaciones implementadas a través de Azure RemoteApp y no, por ejemplo, para una aplicación que se ejecute directamente en el equipo de un usuario y que obtenga acceso a través de Internet pública. Azure RemoteApp se suele considerar el entorno seguro y administrado de forma centralizada y, por lo tanto, la única ruta de acceso a través de la cual los usuarios deben interactuar con el recurso de back-end.

La solución consiste en colocar el entorno de Azure RemoteApp y el recurso seguro en la misma red virtual (VNET) de Azure. Si el recurso está en un sitio diferente, puede establecer una conexión VPN de sitio a sitio, por ejemplo, para crear una red virtual que abarque el centro de datos de Azure y el entorno local del cliente.

Azure RemoteApp admite dos tipos de implementaciones de colección, donde puede proporcionar su propia red virtual:

-	No unida al dominio: las aplicaciones tendrán una "línea de visión" de los otros recursos en la red virtual. Por ejemplo, esta implementación puede usarse para conectar aplicaciones a una base de datos SQL que utiliza autenticación de SQL (las aplicaciones se autentican al usuario directamente en la base de datos).

-	Unida al dominio: las máquinas virtuales que usa Azure RemoteApp se unen a un controlador de dominio en la red virtual. Esto es útil cuando las aplicaciones deben autenticarse en un controlador de dominio de Windows para acceder a un recurso de back-end. ![Colección unida a dominio de Azure RemoteApp](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### Cómo crear una conexión segura entre Azure y mi entorno local
Hay varias opciones de configuración para la conexión de los entornos de Azure y local. Aquí encontrará una buena descripción general de las opciones.

Con Azure RemoteApp debe configurar primero su red virtual y, después, utilizarla durante el proceso de creación de la colección.

## La solución completa
El diagrama siguiente muestra la solución completa donde hemos creado un canal de acceso seguro del usuario final, a través de Azure RemoteApp (ARA), al recurso de back-end. ![Azure RemoteApp seguro](./media/remoteapp-secureaccess/ra-secureoverview.png) En la fase 1, hemos seleccionado a los usuarios y hemos creado reglas de acceso que rigen cómo se puede acceder a ARA. En el ejemplo siguiente solo se permite el acceso a los usuarios que trabajan en la red corporativa. Los usuarios no conformes no podrá acceder de ninguna manera al entorno de ARA. En la "fase 2" se ha expuesto el recurso de back-end solo a través de la configuración de red virtual o VPN que se controla. Azure RemoteApp se ha colocado en la misma red virtual. El resultado final es que solo se puede acceder al recurso a través del entorno de ARA.

<!---HONumber=AcomDC_0121_2016-->