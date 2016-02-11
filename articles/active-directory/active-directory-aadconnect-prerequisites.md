<properties
   pageTitle="Azure AD Connect: Requisitos previos y hardware | Microsoft Azure"
   description="En este tema se describen los requisitos previos y los requisitos de hardware de Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell;billmath"/>

# Requisitos previos de Azure AD Connect
En este tema se describen los requisitos previos y los requisitos de hardware de Azure AD Connect.

## Antes de instalar Azure AD Connect
Antes de instalar Azure AD Connect, hay algunas cosas que necesitará.

### Azure AD
- Una suscripción de Azure o una [suscripción de prueba de Azure](https://azure.microsoft.com/pricing/free-trial/) Solo es necesario para el acceso al Portal de Azure, no para usar Azure AD Connect. Si usa PowerShell u Office 365 no es necesario utilizar una suscripción de Azure para usar Azure AD Connect. Si tiene una licencia de Office 365 también puede utilizar el portal de Office 365. Con una licencia de Office 365 de pago también puede entrar en el portal de Azure desde el portal de Office 365.
- [Agregue y compruebe el dominio](active-directory-add-domain.md) que pretende usar en Azure AD. Por ejemplo, si tiene previsto usar contoso.com para los usuarios, asegúrese de que este dominio se ha comprobado y no usa solamente el dominio predeterminado contoso.onmicrosoft.com.
- De forma predeterminada, un directorio de Azure AD permitirá 50.000 objetos. Al comprobar el dominio, el límite se incrementará a 300 000 objetos. Si todavía necesita más objetos en Azure AD, tiene que abrir una incidencia para aumentar el límite aún más. Si necesita más de 500.000 objetos, necesitará una licencia como Office 365, Azure AD Básico, Azure AD Premium o Enterprise Mobility Suite.

### Entorno y servidores locales
- La versión del esquema de AD y el nivel funcional del bosque deben ser Windows Server 2003 o una versión posterior. Los controladores de dominio pueden ejecutar cualquier versión siempre que se cumplan los requisitos de nivel de bosque y esquema.
- Si pretende usar la característica de **escritura diferida de contraseñas**, los controladores de dominio deben estar en Windows Server 2008 (con el SP más reciente) o posterior. Si los controladores de dominio están en 2008 (antes de la versión R2), también debe aplicar la [revisión KB2386717](http://support.microsoft.com/kb/2386717).
- Azure AD Connect no puede instalarse en Small Business Server o Windows Server Essentials. El servidor debe usar Windows Server estándar o una versión superior.
- Azure AD Connect debe instalarse en Windows Server 2008 o en una versión superior. Este servidor puede ser un controlador de dominio o un servidor miembro si se usa la configuración rápida. Si usa la configuración personalizada, el servidor también puede ser independiente y no tiene que estar unido a un dominio.
- Si instala Azure AD Connect en Windows Server 2008, asegúrese de aplicar las revisiones más recientes de Windows Update. La instalación no se podrá iniciar con un servidor sin revisiones.
- Si pretende usar la característica de **sincronización de contraseñas**, el servidor de Azure AD Connect debe estar en Windows Server 2008 R2 SP1 o posterior.
- El servidor de Azure AD Connect debe tener instalado [.NET Framework 4.5.1](#component-prerequisites) o versiones posteriores y [Microsoft PowerShell 3.0](#component-prerequisites) o versiones posteriores.
- Si se implementa Servicios de federación de Active Directory, los servidores en los que se instale AD FS o el Proxy de aplicación web deben ser Windows Server 2012 R2 o versiones posteriores. [Administración remota de Windows](#windows-remote-management) debe estar habilitada en estos servidores para la instalación remota.
- Si se implementa Servicios de federación de Active Directory, necesita [Certificados SSL](#ssl-certificate-requirements).
- Azure AD Connect requiere una base de datos de SQL Server para almacenar datos de identidad. De forma predeterminada se instala SQL Server 2012 Express LocalDB (una versión ligera de SQL Server Express) y se crea la cuenta de servicio para el servicio en el equipo local. SQL Server Express tiene un límite de tamaño de 10 GB que le permite administrar aproximadamente 100 000 objetos. Si tiene que administrar un volumen superior de objetos de directorio, es necesario que el asistente para la instalación apunte a otra instalación de SQL Server. Azure AD Connect admite todas las versiones de Microsoft SQL Server de SQL Server 2008 (con Service Pack 4) a SQL Server 2014. **No se admite** Base de datos SQL de Microsoft Azure como base de datos.

### Cuentas
- Una cuenta de administrador global de Azure AD para el directorio de Azure AD con el que desea realizar la integración. Debe ser una **cuenta profesional o educativa** y no puede ser una **cuenta Microsoft**.
- Una cuenta de administrador de empresa para su Active Directory local si usa la configuración rápida o actualiza desde DirSync.
- [Cuentas es Active Directory](active-directory-aadconnect-accounts-permissions.md) si usa la ruta de acceso de instalación de la configuración personalizada.

### Conectividad
- Si tiene firewalls en la intranet y necesita abrir puertos entre los servidores de Azure AD Connect y los controladores de dominio, consulte [Azure AD Connect Ports](active-directory-aadconnect-ports.md) (Puertos de Azure AD Connect) para más información.
- Si el proxy limita a qué direcciones URL se puede acceder, las direcciones URL que se documentan en [URL de Office 365 e intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) deben abrirse en el proxy.
- Si usa un proxy de salida para la conexión a Internet, se debe agregar la siguiente configuración del archivo **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** para que el asistente para la instalación y Azure AD Connect se puedan conectar a Internet y a Azure AD. Este texto debe escribirse en la parte inferior del archivo. En este código, &lt;PROXYADRESS&gt; representa el nombre de host o la dirección IP de proxy real.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Si el servidor proxy requiere autenticación, la sección debe ser similar a lo siguiente.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Con este cambio en el archivo machine.config, el asistente para instalación y el motor de sincronización responderán a las solicitudes de autenticación del servidor proxy. En todas las páginas del asistente para instalación, excepto la página **Configurar**, se usan las credenciales del usuario que ha iniciado sesión. En la página **Configurar** al final del asistente para instalación, el contexto se cambia a la [cuenta de servicio](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) que se creó. Para obtener más información sobre el [elemento de proxy predeterminado](https://msdn.microsoft.com/library/kd3cf2ex.aspx), consulte MSDN.

- También debe configurar winhttp. Inicie un símbolo de comandos y escriba:

```
C:\>netsh
netsh>winhttp
netsh winhttp>set proxy <PROXYADDRESS>:<PROXYPORT>
```

Si tiene problemas de conectividad, consulte [Solución de problemas de conectividad con Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).

### Otros
- Opcional: una cuenta de usuario de prueba para comprobar la sincronización.

## Requisitos previos de los componentes
Azure AD Connect depende de Microsoft PowerShell y .NET Framework 4.5.1. Dependiendo de la versión de Windows Server, haga lo siguiente:

- Windows Server 2012R2
  - Microsoft PowerShell se instala de forma predeterminada, no se requiere ninguna acción.
  - .NET Framework 4.5.1 y versiones posteriores se ofrecen mediante Windows Update. Asegúrese de que ha instalado las actualizaciones más recientes de Windows Server en el Panel de Control.
- Windows Server 2008R2 y Windows Server 2012
  - La versión más reciente de Microsoft PowerShell está disponible en **Windows Management Framework 4.0**, que encontrará en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
  - .NET Framework 4.5.1 y versiones posteriores están disponibles en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La versión más reciente admitida de PowerShell está disponible en **Windows Management Framework 3.0**, que encontrará en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).
 - .NET Framework 4.5.1 y versiones posteriores están disponibles en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads).

## Requisitos previos para la instalación y la configuración de la federación

### Administración remota de Windows
Al utilizar Azure AD Connect para implementar los Servicios de federación de Active Directory (AD FS) o el Proxy de aplicación web, compruebe los requisitos siguientes para garantizar la conectividad y la configuración se completará correctamente.

- Si el servidor de destino está unido al dominio, compruebe que está habilitada la opción Administración remota de Windows.
    - En una ventana de comandos PSH con privilegios elevados, use el comando `Enable-PSRemoting –force`.
- Si el servidor de destino es un equipo WAP no unido al dominio, hay un par de requisitos adicionales.
 	- En el equipo de destino (equipo WAP):
         - Asegúrese de que winrm (Administración remota de Windows / WS-Management) se está ejecutando mediante el complemento Servicios.
         - En una ventana de comandos PSH con privilegios elevados, use el comando `Enable-PSRemoting –force`.
    - En el equipo en el que se está ejecutando el asistente (si el equipo de destino no está unido al dominio o el dominio no es de confianza):
        - En una ventana de comandos PSH con privilegios elevados, use el comando `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`.
 	    - En el Administrador de servidores:
 		     - Agregue el host WAP de DMZ al grupo de máquinas (pestaña Administrador de servidores -> Administrar -> Agregar servidores... usar DNS)
 		     - Pestaña Todos los servidores del Administrador de servidores: haga clic con el botón derecho en el servidor WAP y elija Administrar como..., escriba credenciales locales (no de dominio) para la máquina WAP.
 		     - Para validar la conectividad remota de PSH, en la pestaña Todos los servidores del Administrador de servidores, haga clic con el botón derecho en el servidor WAP y elija Windows PowerShell. Debe abrirse una sesión remota de PSH para asegurarse de que se pueden establecer sesiones remotas de PowerShell.

### Requisitos del certificado SSL
**Importante:** se recomienda encarecidamente utilizar el mismo certificado SSL en todos los nodos de la granja de AD FS, así como todos los servidores del Proxy de aplicación web.

- El certificado debe ser del tipo X 509.
- Puede usar un certificado autofirmado en servidores de federación en un entorno de laboratorio de pruebas. Sin embargo, para un entorno de producción, se recomienda obtener el certificado de una CA pública.
    - Si usa un certificado que no es de confianza pública, asegúrese de que el certificado instalado en cada servidor del Proxy de aplicación web sea de confianza tanto en el servidor local como en todos los servidores de federación.
- La identidad del certificado debe coincidir con el nombre del servicio de federación (por ejemplo, fs.contoso.com).
    - La identidad es una extensión de nombre alternativo del firmante (SAN) de tipo dNSName, o bien, si no hay ninguna entrada de SAN, el nombre del firmante se especifica como un nombre común.  
    - Puede haber varias entradas de SAN en el certificado, siempre que una de ellas coincida con el nombre de servicio de federación.
    - Si piensa usar Unión al lugar de trabajo, se requiere un SAN adicional con el valor **enterpriseregistration.** seguido del sufijo de nombre principal de usuario (UPN) de su organización, por ejemplo, **enterpriseregistration.contoso.com**.
- No se admiten certificados basados en claves CryptoAPI Next Generation (CNG) ni en proveedores de almacenamiento de claves. Esto significa que debe utilizar un certificado basado en un CSP (proveedor de servicios criptográficos) y no en un KSP (proveedor de almacenamiento de claves).
- Se admiten certificados comodín.

## Componentes de soporte de Azure AD Connect
La siguiente es una lista de componentes que Azure AD Connect instalará en el servidor en el que está instalado Azure AD Connect. Esta lista es para una instalación rápida básica. Si decide usar un SQL Server diferente en la página Instalar servicios de sincronización, SQL Express LocalDB no se instalará localmente.

- Utilidades de línea de comandos de Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Módulo de Active Directory para Windows PowerShell
- Microsoft Online Services - Ayudante para el inicio de sesión para profesionales de TI
- Paquete de redistribución de Microsoft Visual C++ 2013

## Requisitos de hardware de Azure AD Connect
La tabla siguiente muestra los requisitos mínimos de un equipo con sincronización de Azure AD Connect.

| Cantidad de objetos en Active Directory | CPU | Memoria | Tamaño de disco duro |
| ------------------------------------- | --- | ------ | --------------- |
| Menos de 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10\.000–50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50\.000–100.000 | 1,6 GHz | 16 GB | 100 GB* |
| Para 100.000 o más objetos, se requiere la versión completa de SQL Server| | | |
| 100\.000–300.000 | 1,6 GHz | 32 GB | < 300 GB |
| 300\.000–600.000 | 1,6 GHz | 32 GB | 450 GB |
| Más de 600.000 | 1,6 GHz | 32 GB | 500 GB |

Los requisitos mínimos para equipos que ejecutan AD FS o servidores de aplicaciones web son los siguientes:

- CPU: 1,6 GHz con doble núcleo o superior
- MEMORIA: 2 GB o superior
- VM de Azure: configuración A2 o superior

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->