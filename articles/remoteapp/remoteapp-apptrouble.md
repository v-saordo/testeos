<properties 
    pageTitle="Solución de problemas de Azure RemoteApp: errores de inicio y conexión de la aplicación | Microsoft Azure" 
    description="Aprenda a solucionar los problemas de inicio y conexión de las aplicaciones en Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/02/2016" 
    ms.author="elizapo" />



#Solución de problemas de Azure RemoteApp: errores de inicio y conexión de la aplicación 

Es posible que las aplicaciones hospedadas en Azure RemoteApp no se inicien por diversos motivos. En este artículo se describe varias razones y mensajes de error que los usuarios pueden recibir cuando intentan iniciar las aplicaciones. También se incluyen los errores de conexión. (Sin embargo, en este artículo no se describen los problemas al iniciar sesión en el cliente de Azure RemoteApp).

Para obtener información sobre los mensajes de error comunes debidos a errores de inicio y conexión de la aplicación, siga leyendo.

##Estamos realizando la configuración... Inténtelo de nuevo en 10 minutos.

Este error significa que Azure RemoteApp se está escalando horizontalmente para satisfacer las necesidades de capacidad de los usuarios. Se están creando en segundo plano más instancias de máquina virtual de Azure RemoteApp con el fin de controlar las necesidades de capacidad de los usuarios. Normalmente, esta operación puede tardar entre cinco y diez minutos. En ocasiones, no se produce lo suficientemente rápido y se necesitan recursos inmediatamente. Por ejemplo, un escenario de 9 de la mañana donde muchos usuarios necesitan usar la aplicación en Azure RemoteAppn al mismo tiempo. Si esto sucede podemos habilitar el **modo de capacidad** en el back-end. Para ello, abra una incidencia de soporte técnico de Azure o envíenos un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Asegúrese de incluir el id. de suscripción en la solicitud.

![Estamos realizando la configuración](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## No ha sido posible la reconexión automática a sus aplicaciones, reinícielas  

Este mensaje de error suele aparecer si estaba usando Azure RemoteApp y pone su PC en suspensión durante más de cuatro horas y a continuación lo reactiva y el cliente de Azure RemoteApp intenta volver a conectarse automáticamente pero el tiempo de espera se ha superado. Indique a los usuarios que vuelvan a la aplicación e intenten abrirla desde el cliente de Azure RemoteApp.

![No ha sido posible la reconexión automática a sus aplicaciones](./media/remoteapp-apptrouble/ra-apptrouble2.png)

## Problemas con el perfil temporal 

Este error se produce cuando el perfil de usuario (disco de perfil de usuario) no se puede montar y el usuario recibe un perfil temporal. Los administradores deben dirigirse a la colección en el Portal de Azure y luego ir a la pestaña **Sesiones** e intentar cerrar la sesión del usuario con **Cerrar sesión**. Esto obligará a un cierre completo de la sesión del usuario. A continuación, indique el usuario que intente iniciar la aplicación de nuevo. Si se produce un error, póngase en contacto con el soporte técnico de Azure o envíenos un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## Azure RemoteApp ha dejado de funcionar

Este mensaje de error significa que el cliente de Azure RemoteApp está teniendo un problema y debe reiniciarse. Indique a los usuarios que cierren la aplicación. Deben seleccionar **Cerrar programa** y luego iniciar de nuevo el cliente de Azure RemoteApp. Si el problema continúa, abra una incidencia de soporte técnico de Azure o envíenos un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp ha dejado de funcionar](./media/remoteapp-apptrouble/ra-apptrouble3.png)

## Se produjo un error mientras Conexión a Escritorio remoto trataba de obtener acceso a este recurso. Vuelva a intentar la conexión o póngase en contacto con el administrador del sistema.

Se trata de un mensaje de error genérico: póngase en contacto con el soporte técnico de Azure o envíenos un correo electrónico a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) para que podamos investigarlo.

![Mensaje genérico de Azure RemoteApp](./media/remoteapp-apptrouble/ra-apptrouble4.png)

<!---HONumber=AcomDC_0211_2016-->