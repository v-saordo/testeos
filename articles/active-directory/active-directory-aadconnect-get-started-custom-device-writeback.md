<properties
	pageTitle="Habilitación de escritura diferida de dispositivos en Azure AD Connect | Microsoft Azure"
	description="En este documento se describe cómo habilitar la escritura diferida de dispositivo con Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="StevenPo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/18/2016"
	ms.author="billmath;andkjell"/>

# Azure AD Connect: Habilitación de escritura diferida de dispositivos

>[AZURE.NOTE] Se necesita una suscripción a Azure AD Premium para la reescritura de dispositivos.

En la siguiente documentación se ofrece información sobre cómo habilitar la característica de escritura diferida de dispositivo en Azure AD Connect. La escritura diferida de dispositivo se usa en los siguientes escenarios:

- Habilite el acceso condicional basado en dispositivos para aplicaciones protegida de ADFS (2012 R2 o superior) (relaciones de confianza para usuario autenticado).

Esto ofrece seguridad adicional y la garantía de que el acceso a las aplicaciones solo se concede para dispositivos de confianza. Para obtener más información sobre el acceso condicional, consulte [Administración de riesgos con el acceso condicional](active-directory-conditional-access.md) y [Configuración del acceso condicional local mediante el Registro de dispositivos de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.IMPORTANT]
<li>Los dispositivos deben encontrarse en el mismo bosque que los usuarios. Puesto que los dispositivos deben volver a escribirse en un único bosque, esta característica no admite actualmente una implementación con varios bosques de usuarios.</li> <li>Solo se puede agregar un objeto de configuración de registro de dispositivo al bosque de Active Directory local. Esta característica no es compatible con una topología en la que el Active Directory local está sincronizado a varios directorios de Azure AD.</li>

## Parte 1: Instalación de Azure AD Connect
1. Instale Azure AD Connect mediante la configuración rápida o personalizada. Para empezar, se recomienda tener sincronizados correctamente todos los usuarios y grupos antes de habilitar la reescritura de dispositivos.

## Parte 2: Preparación de Active Directory
Lleve a cabo los siguientes pasos para prepararse para usar la reescritura de dispositivos.

1.	Desde el equipo donde está instalado Azure AD Connect, inicie PowerShell en modo elevado.

2.	Si el módulo de Active Directory PowerShell NO está instalado, instálelo mediante el siguiente comando:

	`Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Si NO está instalado el módulo de Azure Active Directory PowerShell, descárguelo e instálelo desde el [módulo de Azure Active Directory para Windows PowerShell (versión de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Este componente tiene una dependencia en el Ayudante para el inicio de sesión, que se instala con Azure AD Connect.

4.	Con credenciales de administrador de organización, ejecute los comandos siguientes y luego salga de PowerShell.

	`Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

	`Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

Se necesitan credenciales de administrador de organización, puesto que es necesario realizar cambios en el espacio de nombres de configuración. Un administrador de dominio no tiene suficientes permisos.

![PowerShell](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Description:

- En caso de no existir, crea y configura nuevos contenedores y objetos en CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
- En caso de no existir, crea y configura nuevos contenedores y objetos en CN=RegisteredDevices,[domain-dn]. Los objetos de dispositivo se crearán en este contenedor.
- Establece los permisos necesarios en la cuenta de Azure AD Connector, para administrar dispositivos en su Active Directory.
- Solo necesita ejecutarse en un bosque, incluso si se está instalando Azure AD Connect en varios bosques.

Parámetros:

- DomainName: dominio de Active Directory donde se crearán los objetos de dispositivo. Nota: todos los dispositivos para un bosque de Active Directory determinado se creará en un dominio único.
- AdConnectorAccount: la cuenta de Active Directory que usará Azure AD Connect para administrar objetos en el directorio. Es la cuenta que usa Azure AD Connect Sync para conectarse a AD. Si realizó la instalación mediante la configuración rápida, es la cuenta con el prefijo MSOL\_.

## Parte 3: Habilitación de la reescritura de dispositivos en Azure AD Connect
Lleve a cabo el siguiente procedimiento para habilitar la reescritura de dispositivos en Azure AD Connect.

1.	Ejecute de nuevo el Asistente para la instalación. Seleccione **Personalizar las opciones de sincronización** en la página Tareas adicionales y haga clic en **Siguiente**. ![Instalación personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.	En la página Características opcionales, la reescritura de dispositivos ya no estará atenuada. Tenga en cuenta que si no se completan los pasos preparatorios de Azure AD Connect, la reescritura de dispositivos se atenuará en la página de características opcionales. Active la casilla para la escritura diferida de dispositivos y haga clic en **Siguiente**. Si la casilla sigue desactivada, vea la [sección de solución de problemas](#the-writeback-checkbox-is-still-disabled). ![Escritura diferida de dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.	En la página de escritura diferida, verá el dominio suministrado como el bosque de escritura diferida de dispositivos predeterminado. ![Instalación personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.	Complete la instalación del asistente sin realizar ningún cambio de configuración adicional. En caso necesario, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

## Habilitar el acceso condicional
Encontrará a su disposición instrucciones detalladas para habilitar este escenario en [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## Comprobar que los dispositivos están sincronizados con Active Directory
La reescritura de dispositivos debería funcionar ahora correctamente. Tenga en cuenta que se puede tardar hasta tres horas en que los objetos de dispositivos se vuelvan a escribir en AD. Para comprobar que los dispositivos que se están sincronizados correctamente, siga este procedimiento después de completar las reglas de sincronización:

1.	Inicie el Centro de administración de Active Directory.
2.	Expanda RegisteredDevices dentro del dominio que se está federando. ![Instalación personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.	Los dispositivos registrados actuales aparecerá en la lista.

![Instalación personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## Solución de problemas

### La casilla de reescritura sigue deshabilitada
Si la casilla para la reescritura de dispositivos no se habilita a pesar de haber seguido los pasos anteriores, los siguientes pasos le guiarán por lo que el Asistente para la instalación comprueba antes de habilitar la casilla.

En primer lugar:

- Asegúrese de que al menos un bosque tenga Windows Server 2012 R2. Debe existir el tipo de objeto de dispositivo.
- Si el Asistente para la instalación ya se está ejecutando, algunos cambios no se detectarán. En este caso, complete el Asistente para la instalación y ejecútelo de nuevo.
- Asegúrese de que la cuenta que proporciona en el script de inicialización sea realmente la del usuario correcto usado por Active Directory Connector. Para ello, siga estos pasos:
	- En el menú Inicio, abra **Servicio de sincronización**.
	- Abra la pestaña **Conectores**.
	- Busque el conector con el tipo Dominio de Active Directory y selecciónelo.
	- En **Acciones**, seleccione **Propiedades**.
	- Vaya a **Conexión al bosque de Active Directory**. Compruebe que el dominio y el nombre de usuario especificados en esta pantalla coinciden con la cuenta proporcionada para el script. ![Cuenta de conector](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Compruebe la configuración de Active Directory:-Compruebe que el servicio de registro del dispositivo se encuentre en la siguiente ubicación (CN=DeviceRegistrationService,CN=Servicios de registro del dispositivo,CN=Configuración de registro del dispositivo,CN=Servicios,CN=Configuración) en el contexto de nomenclatura de configuración.

![Solución de problemas1](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Compruebe que haya un solo objeto de configuración; para ello, busque en el espacio de nombres de configuración. Si hay más de uno, elimine el duplicado.

![Solución de problemas2](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- En el objeto Servicio de registro del dispositivo, asegúrese de que exista el atributo msDS-DeviceLocation y que tenga un valor. Busque en esta ubicación y asegúrese de que exista con el tipo de objeto msDS-DeviceContainer.

![Solución de problemas3](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Solución de problemas4](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Compruebe que la cuenta usada por el conector de Active Directory tenga los permisos necesarios en el contenedor Dispositivos registrados encontrado mediante el paso anterior. Este es el permiso esperado en este contenedor:

![Solución de problemas5](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Compruebe que la cuenta de Active Directory tenga permisos en el objeto CN=Configuración de registro del dispositivo, CN=Servicios, CN=Configuración.

![Solución de problemas6](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## Información adicional
- [Administración de riesgos con el acceso condicional](active-directory-conditional-access.md)
- [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->