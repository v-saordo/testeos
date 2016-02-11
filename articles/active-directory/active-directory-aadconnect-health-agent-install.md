<properties
	pageTitle="Instalación del agente de Azure AD Connect Health | Microsoft Azure"
	description="Página de Azure AD Connect Health que describe la instalación del agente para AD FS y sincronización."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath"/>






# Instalación del agente de Azure AD Connect Health 

Este documento le guiará en la instalación y configuración del agente de Azure AD Connect Health para AD FS y sincronización.

>[AZURE.NOTE]Recuerde que para ver los datos AD FS de la instancia de Azure AD Connect Health, deberá instalar el agente de Azure AD Connect Health en los servidores de destino. Asegúrese de completar los requisitos que se describen [aquí](active-directory-aadconnect-health.md#requirements) antes de instalar el agente. Puede descargar el agente [aquí](http://go.microsoft.com/fwlink/?LinkID=518973).


## Instalación del agente de Azure AD Connect Health para AD FS
Para iniciar la instalación del agente, haga doble clic en el archivo .exe que descargó. En la primera pantalla, haga clic en Instalar.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Una vez que finalice la instalación, haga clic en Configurar ahora.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Con esto se iniciará un símbolo del sistema seguido de una instancia de PowerShell que ejecutará Register-AzureADConnectHealthADFSAgent. Se le pedirá que inicie sesión en Azure. Continúe e inicie sesión.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Después de iniciar sesión, PowerShell continuará. Una vez que termine, puede cerrar PowerShell y la configuración estará completa.

En este punto, los servicios se iniciarán automáticamente y el agente supervisará y recopilará datos. Tenga en cuenta que si no ha cumplido todos los requisitos previos descritos en las secciones anteriores, verá advertencias en la ventana de PowerShell. Asegúrese de completar los requisitos que se describen [aquí](active-directory-aadconnect-health.md#requirements) antes de instalar el agente. La captura de pantalla siguiente es un ejemplo de estos errores.

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para comprobar que se instaló el agente, abra los servicios y busque lo siguiente. Si completó la configuración, estos servicios deben aparecer en ejecución. De lo contrario, no se iniciarán hasta que se complete la configuración.

- Servicio de diagnóstico de AD FS de Azure AD Connect Health
- Servicio de análisis de AD FS de Azure AD Connect Health
- Servicio de supervisión de AD FS de Azure AD Connect Health

![Comprobación de Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


### Instalación del agente en servidores de Windows Server 2008 R2

En el caso de los servidores de Windows Server 2008 R2, haga lo siguiente:

1. Asegúrese de que se esté ejecutando el Service Pack 1 o posterior del servidor.
1. Desactive ESC de Internet Explorer para la instalación del agente:
1. Instale Windows PowerShell 4.0 en cada uno de los servidores antes de instalar el agente de AD Health. Para instalar Windows PowerShell 4.0:
 - Instale [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) mediante el vínculo siguiente para descargar el instalador sin conexión.
 - Instale PowerShell ISE (desde Características de Windows)
 - Instale [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Instale Internet Explorer versión 10 o posterior en el servidor. Esto es necesario para que el servicio de mantenimiento autentique su identidad con las credenciales de administrador de Azure.
1. Para obtener información adicional acerca de cómo instalar Windows PowerShell 4.0 en Windows Server 2008 R2, consulte el artículo wiki [aquí](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### Habilitación de la auditoría de AD FS

Para que la característica Análisis de uso pueda recopilar y analizar datos, el agente de Azure AD Connect Health necesita la información de los registros de auditoría de AD FS. que no están habilitados de forma predeterminada. Esto solo se aplica a los servidores de federación de AD FS. No es necesario habilitar la auditoría en los servidores proxy de AD FS o de aplicación web. Use los procedimientos siguientes para habilitar la auditoría de AD FS y localizar los registros de auditoría de AD FS obtenidos.

#### Para habilitar la auditoría de AD FS 2.0

1. Haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en **Directiva de seguridad local**.
2. Navegue hasta la carpeta **Configuración de seguridad\\Directivas locales\\Administración de derechos de usuario** y haga doble clic en Generar auditorías de seguridad.
3. En la pestaña **Configuración de seguridad local**, compruebe que aparezca la cuenta de servicio de AD FS 2.0. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
4. Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Cierre Directiva de seguridad local y luego abra el complemento de administración. Para abrir el complemento de administración, haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en Administración de AD FS 2.0.
6. En el panel Acciones, haga clic en Editar propiedades del servicio de federación.
7. En el cuadro de diálogo **Propiedades del servicio de federación**, haga clic en la pestaña **Eventos**.
8. Active las casillas **Auditorías de aciertos** y **Auditorías de errores**.
9. Haga clic en **Aceptar**.

#### Para habilitar la auditoría de AD FS en Windows Server 2012 R2

1. Abra **Directiva de seguridad local**; para ello, abra **Administrador del servidor** en la pantalla Inicio o Administrador del servicio en la barra de tareas del escritorio y luego haga clic en **Herramientas/Directiva de seguridad local**.
2. Navegue hasta la carpeta **Configuración de seguridad\\Directivas locales\\Asignación de derechos de usuario** y haga doble clic en **Generar auditorías de seguridad**.
3. En la pestaña **Configuración de seguridad local**, compruebe que aparezca la cuenta de servicio de AD FS. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
4. Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Cierre **Directiva de seguridad local** y luego abra el complemento **Administración de AD FS** (en Administrador del servidor, haga clic en Herramientas y luego seleccione Administración de AD FS).
6. En el panel Acciones, haga clic en **Editar propiedades del servicio de federación**.
7. En el cuadro de diálogo Propiedades del servicio de federación, haga clic en la pestaña **Eventos**.
8. Active las casillas **Auditorías de aciertos y Auditorías de errores** y luego haga clic en **Aceptar**.






#### Para buscar los registros de auditoría de AD FS


1. Abra **Visor de eventos**.
2. Vaya a Registros de Windows y seleccione **Seguridad**.
3. A la derecha, haga clic en **Filtrar registros actuales**.
4. En Origen de evento, seleccione **Auditoría de AD FS**.

![Registros de auditoría de AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Si tiene una directiva de grupo que deshabilita la auditoría de AD FS, el agente de Azure AD Connect Health no podrá recopilar información. Asegúrese de no tener ninguna directiva de grupo de este tipo.

[//]: # "Inicio de la sección de configuración del proxy del agente"

## Instalación del agente de Azure AD Connect Health para sincronización
El agente de Azure AD Connect Health para sincronización se instala automáticamente en la última compilación de Azure AD Connect. Para usar Azure AD Connect para sincronización, tendrá que descargar la versión más reciente de Azure AD Connect e instalarla. Puede descargar la versión más reciente [aquí](http://www.microsoft.com/download/details.aspx?id=47594).

Para comprobar que se instaló el agente, abra los servicios y busque lo siguiente. Si completó la configuración, estos servicios deben aparecer en ejecución. De lo contrario, no se iniciarán hasta que se complete la configuración.

- Servicio de análisis de AADSync de Azure AD Connect Health
- Servicio de supervisión de AADSync de Azure AD Connect Health
 
![Comprobación de Azure AD Connect Health para sincronización](./media/active-directory-aadconnect-health-sync/services.png)

>[Azure.NOTE] Recuerde que el uso de Azure AD Connect Health requiere Azure AD Premium. Si no tiene Azure AD Premium no podrá completar la configuración en el portal de Azure. Para obtener más información, consulte los requisitos [aquí](active-directory-aadconnect-health.md#requirements).




## Configuración de agentes de Azure AD Connect Health para usar el proxy HTTP
Puede configurar agentes de Azure AD Connect Health para trabajar con un proxy HTTP

>[AZURE.NOTE]
- El uso de "Netsh WinHttp set ProxyServerAddress" no funcionará ya que el agente utiliza System.Net para realizar solicitudes web en lugar de los servicios HTTP de Microsoft Windows. - La dirección del proxy Http configurada se utilizará para pasar mensajes Https cifrados. - No se admiten servidores proxy autenticados (mediante HTTPBasic).

### Cambio de configuración del proxy del agente de estado
Tiene las siguientes opciones para configurar el agente de Azure AD Connect Health para utilizar a un proxy HTTP.

>[AZURE.NOTE] Debe reiniciar todos los servicios del agente de Azure AD Connect Health para la configuración de proxy. Ejecute el siguiente comando:<br> Restart-Service AdHealth*

#### Importación de configuración de proxy existente

##### Importación desde Internet Explorer.
Puede importar la configuración del proxy HTTP de Internet Explorer y usarla para agentes de Azure AD Connect Health ejecutando el siguiente comando de PowerShell en cada servidor en el que se ejecute el agente de estado.

	Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importación desde WinHTTP
Puede importar la configuración del proxy WinHTTP ejecutando el siguiente comando de PowerShell en cada servidor en el que se ejecute el agente de estado.

	Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Especificación manual de direcciones de proxy
Puede especificar un servidor proxy manualmente ejecutando el siguiente comando de PowerShell en cada servidor en el que se ejecute el agente de estado.

	Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Ejemplo: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myservidorproxy:443*

- "address" puede ser un nombre de servidor resoluble DNS o una dirección IPv4
- "port" se puede omitir. Si se omite, se elige 443 como puerto predeterminado.

#### Borrado de la configuración de proxy existente
Puede borrar la configuración de proxy existente ejecutando el comando siguiente.

	Set-AzureAdConnectHealthProxySettings -NoProxy


### Lectura de la configuración de proxy actual
Puede utilizar el comando siguiente para leer la configuración de proxy definida actualmente.

	Get-AzureAdConnectHealthProxySettings


[//]: # "Fin de la sección de configuración del proxy del agente"


## Vínculos relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=AcomDC_0128_2016-->