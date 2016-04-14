<properties 
	pageTitle="Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS" 
	description="Se trata de la página de Azure Multi-Factor Authentication que le ayudará en la implementación de la puerta de enlace de escritorio remoto (RD) y el servidor Azure Multi-Factor Authentication mediante RADIUS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS

En muchos casos, la puerta de enlace de escritorio remoto usa el NPS local para autenticar usuarios. En este documento se describe cómo enrutar la solicitud RADIUS desde la puerta de enlace de Escritorio remoto (a través del NPS local) al servidor Multi-Factor Authentication.

El servidor Multi-Factor Authentication debe instalarse en un servidor independiente, que luego entregará la solicitud RADIUS a NPS en el servidor de puerta de enlace de escritorio remoto. Cuando NPS valide el nombre de usuario y la contraseña, devolverá una respuesta al servidor Multi-Factor Authentication que realiza el segundo factor de la autenticación antes de devolver un resultado a la puerta de enlace.





## Configuración de la puerta de enlace de escritorio remoto

La puerta de enlace de escritorio remoto debe configurarse para enviar autenticación RADIUS a un servidor de Azure Multi-Factor Authentication. En cuanto se instale, se configure y se ponga en funcionamiento la puerta de enlace de escritorio remoto, acceda a las propiedades de la puerta de enlace de escritorio remoto. Vaya a la pestaña de Almacén de CAP de RD y cámbielo para usar un servidor central que ejecute NPS en lugar del servidor local que ejecuta NPS. Agregue uno o más servidores de Azure Multi-Factor Authentication como servidores RADIUS y especifique un secreto compartido para cada servidor.





## Configuración de NPS

La puerta de enlace de escritorio remoto usa NPS para enviar la solicitud de RADIUS a la Azure Multi-Factor Authentication. Debe cambiarse un tiempo de espera para evitar superar el tiempo de espera de la puerta de enlace de escritorio remoto antes de que se haya completado la autenticación multifactor. Use el procedimiento siguiente para configurar NPS.

1. En NPS, expanda el menú de clientes RADIUS y servidor de la columna izquierda y haga clic en Grupos de servidores RADIUS remotos. Acceda a las propiedades del GRUPO DE SERVIDORES DE PUERTA DE ENLACE DE TS. Edite los servidores RADIUS que se muestran y vaya a la pestaña de equilibrio de carga. Cambie el "Número de segundos sin respuesta antes de que se considere a la solicitud eliminada" y el "Número de segundos entre solicitudes cuando el servidor se identifica como no disponible" a entre 30 y 60 segundos. Haga clic en la pestaña Autenticación/Cuenta y asegúrese de que los puertos RADIUS especificados coincidan con los puertos en los que estará escuchando el servidor Multi-Factor Authentication.
2. NPS también debe configurarse para recibir autenticaciones RADIUS desde el servidor de Azure Multi-Factor Authentication. Haga clic en Clientes RADIUS en el menú de la izquierda. Agregue el servidor de Azure Multi-Factor Authentication como cliente RADIUS. Elija un nombre descriptivo y especifique un secreto compartido.
3. Expanda la sección de Directivas del panel izquierdo y haga clic en Directivas de solicitud de conexión. Debe contener una directiva de solicitud de conexión denominada DIRECTIVA DE AUTORIZACIÓN DE PUERTA DE ENLACE TS creada al configurar la puerta de enlace RD. Esta directiva reenvía las solicitudes RADIUS al servidor Multi-Factor Authentication.
4. Copie esta directiva para crear una nueva. En la nueva directiva, agregue una condición que haga coincidir el nombre descriptivo del cliente con el nombre descriptivo establecido en el paso 2 anterior para el cliente RADIUS del servidor Azure Multi-Factor Authentication. Cambie el proveedor de autenticación a Equipo Local. Esta directiva garantiza que cuando se recibe una solicitud RADIUS desde el servidor Azure Multi-Factor Authentication, la autenticación se produce localmente en lugar de enviar una solicitud RADIUS al servidor Azure Multi-Factor Authentication que daría lugar a una condición de bucle. Para evitar la condición de bucle, esta nueva directiva debe situarse POR ENCIMA de la directiva original que la reenvía al servidor Multi-Factor Authentication.

## Configuración de Azure Multi-Factor Authentication


--------------------------------------------------------------------------------



El servidor Azure Multi-Factor Authentication se configura como un proxy RADIUS entre la puerta de enlace de escritorio remoto y NPS. Debe instalarse en un servidor unido a un dominio independiente del servidor de puerta de enlace de escritorio remoto. Use el procedimiento siguiente para configurar el servidor Azure Multi-Factor Authentication.

1. Abra el servidor Azure Multi-Factor Authentication y haga clic en el icono de autenticación RADIUS. Marque la casilla de verificación Habilitar autenticación RADIUS.
2. En la pestaña Clientes, asegúrese de que los puertos coincidan con lo que se configura en NPS y haga clic en el botón Agregar... Agregue la dirección IP del servidor de puerta de enlace de escritorio remoto, el nombre de la aplicación (opcional) y un secreto compartido. El secreto compartido deberá ser el mismo en el servidor Azure Multi-Factor Authentication y en la puerta de enlace de escritorio remoto.
3. Haga clic en la pestaña Destino y elija el botón de opción Servidores RADIUS.
4. Haga clic en el botón Agregar... Escriba la dirección IP, el secreto compartido y los puertos del servidor NPS. A menos que use un NPS central, el cliente RADIUS y el destino RADIUS coincidirán. El secreto compartido debe coincidir con el configurado en la sección de cliente RADIUS del servidor NPS. 

![Autenticación Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!---HONumber=AcomDC_0224_2016-->