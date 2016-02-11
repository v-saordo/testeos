<properties
	pageTitle="Visualización de los informes de acceso y uso | Microsoft Azure"
	description="Explica cómo ver informes de acceso y uso para proporcionar visibilidad sobre la integridad y la seguridad del directorio de su organización."
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="kenhoff;Justinha;curtand"/>


# Visualización de los informes de acceso y uso

*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

Puede usar los informes de acceso y uso de Active Directory de Azure para proporcionar visibilidad sobre la integridad y la seguridad del directorio de su organización. Con esta información, un administrador de directorios puede determinar mejor dónde puede haber posibles riesgos de seguridad de modo que pueda planear adecuadamente la mitigación de estos riesgos.

En el Portal de administración de Azure, los informes se clasifican de la manera siguiente:

- Informes de anomalías: contienen eventos de inicio de sesión que consideramos anómalos. Nuestro objetivo es que sea consciente de dicha actividad y que pueda tomar una decisión sobre si un evento es sospechoso.
- Informes de aplicaciones integradas: proporciona información sobre cómo se usan en su organización aplicaciones en la nube. Azure Active Directory ofrece integración con miles de aplicaciones en la nube.
- Informes de errores: indican errores que se pueden producir al aprovisionar cuentas a aplicaciones externas.
- Informes específicos del usuario: muestran los datos de actividad de dispositivo/inicio de sesión para un usuario específico.
- Registros de actividad: contienen un registro de todos los eventos auditados en las últimas 24 horas, los últimos 7 días o los últimos 30 días, así como los cambios de la actividad de grupo y la actividad de registro y restablecimiento de contraseña.

> [AZURE.NOTE]
>
- Algunos informes de uso de recursos y de anomalías avanzados solo están disponibles cuando se habilita [Azure Active Directory Premium](active-directory-get-started-premium.md). Los informes avanzados le ayudan a mejorar la seguridad de acceso, responder a amenazas potenciales y obtener acceso a análisis sobre el uso de aplicaciones y el acceso a dispositivos.
- Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Informes

|	Informe |	Descripción |
|	------												|	-----																						|
|	**Informes de actividades anómalas**
|	[Inicios de sesión desde orígenes desconocidos](active-directory-reporting-sign-ins-from-unknown-sources.md) |	Puede indicar un intento de iniciar sesión sin realizar ningún seguimiento. |
|	[Inicios de sesión tras varios errores](active-directory-reporting-sign-ins-after-multiple-failures.md) |	Puede indicar un ataque por fuerza bruta realizado con éxito. |
|	[Inicios de sesión desde varias ubicaciones geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md) |	Puede indicar que varios usuarios inician una sesión con la misma cuenta. |
|	[Inicios de sesión desde direcciones IP con actividad sospechosa](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |	Puede indicar un inicio de sesión correcto después de un intento de intrusión sostenido. |
|	[Inicios de sesión desde dispositivos posiblemente infectados](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |	Puede indicar un intento de iniciar sesión desde dispositivos posiblemente infectados. |
|	[Actividad de inicio de sesión irregular](active-directory-reporting-irregular-sign-in-activity.md) |	Puede indicar eventos incorrectos en los patrones de inicio de sesión de los usuarios. |
|	[Usuarios con actividad de inicio de sesión erróneo.](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |	Indica los usuarios cuyas cuentas se han comprometido. |
|	Usuarios con credenciales perdidas |	Usuarios con credenciales perdidas |
|	**Registros de actividad**
|	Informe de auditoría |	Eventos auditados en el directorio |
|	Actividad de restablecimiento de contraseña |	Proporciona una vista detallada de los restablecimientos de contraseña que se producen en su organización. |
|	Actividad de registro de restablecimiento de contraseñas |	Proporciona una vista detallada de los registros de restablecimiento de contraseña que se producen en su organización. |
|	Actividad de los grupos de autoservicio |	Proporciona un registro de actividad para la actividad de autoservicio de todo el grupo en su directorio |
|	**Aplicaciones integradas**
|	Uso de la aplicación |	Proporciona un resumen de uso para todas las aplicaciones de SaaS integradas con su directorio. |
|	Actividad de aprovisionamiento de cuentas |	Proporciona un historial de intentos para aprovisionar cuentas en aplicaciones externas. |
|	Estado de la sustitución de contraseña |	Proporciona información detallada del estado de sustitución automática de contraseñas de aplicaciones SaaS. |
|	Errores de aprovisionamiento de cuentas |	Indica un impacto en el acceso de los usuarios a aplicaciones externas. |
|	**Rights management**
|	Uso de RMS |	Proporciona un resumen de uso de Rights Management |
|	Usuarios de RMS más activos |	Enumera los 1.000 usuarios activos principales que han accedido a archivos protegidos mediante RMS |
|	Uso de dispositivos RMS |	Enumera los dispositivos utilizados para obtener acceso a los archivos protegidos mediante RMS |
|	Uso de aplicaciones habilitadas para RMS |	Permite usar aplicaciones habilitadas para RMS |

## Ediciones de los informes

|	Informe |	Gratuito |	Básica |	Premium |
|	------												|	----	|	-----	|	--------	|
|	**Informes de actividades anómalas**
|	Inicios de sesión desde orígenes desconocidos |	✓ |	✓ |	✓ |
|	Inicios de sesión tras varios errores |	✓ |	✓ |	✓ |
|	Inicios de sesión desde varias ubicaciones geográficas |	✓ |	✓ |	✓ |
|	Inicios de sesión desde direcciones IP con actividad sospechosa | | |	✓ |
|	Inicios de sesión desde dispositivos posiblemente infectados | | |	✓ |
|	Actividad de inicio de sesión irregular | | |	✓ |
|	Usuarios con actividad de inicio de sesión erróneo. | | |	✓ |
|	Usuarios con credenciales perdidas | | |	✓ |
|	**Registros de actividad**
|	Informe de auditoría |	✓ |	✓ |	✓ |
|	Actividad de restablecimiento de contraseña | | |	✓ |
|	Actividad de registro de restablecimiento de contraseñas | | |	✓ |
|	Actividad de los grupos de autoservicio | | |	✓ |
|	**Aplicaciones integradas**
|	Uso de la aplicación | | |	✓ |
|	Actividad de aprovisionamiento de cuentas |	✓ |	✓ |	✓ |
|	Estado de la sustitución de contraseña | | |	✓ |
|	Errores de aprovisionamiento de cuentas |	✓ |	✓ |	✓ |
|	**Administración de derechos**
|	Uso de RMS | | |	Solo RMS |
|	Usuarios de RMS más activos | | |	Solo RMS |
|	Uso de dispositivos RMS | | |	Solo RMS |
|	Uso de aplicaciones habilitadas para RMS | | |	Solo RMS |



## Informes de actividades anómalas
<p>Los informes de actividades anómalas de inicio de sesión marcan actividades de inicio de sesión sospechosos en Office365, en el Portal de administración de Azure, el Panel de acceso de Azure AD, Sharepoint Online, Dynamics CRM Online y otros servicios en línea de Microsoft.</p>
<p>Todos estos informes, excepto el informe "Inicios de sesión tras varios errores", también marcan inicios de sesión <i>federados</i> sospechosos en los servicios mencionados anteriormente, independientemente del proveedor de federación. </p>
<p>Están disponibles los siguientes informes: </p><ul> <li>[Inicios de sesión de orígenes desconocidos](active-directory-reporting-sign-ins-from-unknown-sources.md).</li> <li>[Inicios de sesión tras varios errores](active-directory-reporting-sign-ins-after-multiple-failures.md).</li> <li>[Inicios de sesión desde varias ubicaciones geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md).</li> <li>[Inicios de sesión desde direcciones IP con actividad sospechosa](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md).</li> <li>[Actividad de inicio de sesión irregular](active-directory-reporting-irregular-sign-in-activity.md).</li> <li>[Inicios de sesión desde dispositivos posiblemente infectados](active-directory-reporting-sign-ins-from-possibly-infected-devices.md).</li> <li>[Usuarios con actividad de inicio de sesión erróneo](active-directory-reporting-users-with-anomalous-sign-in-activity.md).</li> <li>Usuarios con credenciales perdidas</li></ul>










## Registros de actividad

### Informe de auditoría

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra un registro de todos los eventos auditados en las últimas 24 horas, los últimos 7 días o los últimos 30 días. <br /> Para obtener más información, consulte [Eventos del informe de auditoría de Azure Active Directory](active-directory-reporting-audit-events.md). | Directorio > pestaña Informes |

![Informe de auditoría](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### Actividad de restablecimiento de contraseña

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra los intentos de restablecimiento de contraseña que se han producido en su organización. | Directorio > pestaña Informes |

![Actividad de restablecimiento de contraseña](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### Actividad de registro de restablecimiento de contraseñas

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra los registros de restablecimiento de contraseña que se han producido en su organización. | Directorio > pestaña Informes |

![Actividad de registro de restablecimiento de contraseñas](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### Actividad de los grupos de autoservicio

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra toda la actividad de los grupos administrados de autoservicio en el directorio. | Directorio > Usuarios > <i>Usuario</i> > pestaña Dispositivos |

![Actividad de los grupos de autoservicio](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## Informes de aplicaciones integradas

### Uso de la aplicación: resumen

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Use este informe cuando quiera ver el uso de todas las aplicaciones SaaS en su directorio. Este informe se basa en el número de veces que los usuarios hacen clic en la aplicación en el Panel de acceso. | Directorio > pestaña Informes |

Este informe incluye los inicios de sesión en _todas_ las aplicaciones a las que su directorio tiene acceso, incluidas las aplicaciones previamente integradas de Microsoft.

Entres las aplicaciones previamente integradas de Microsoft se incluyen Office 365, Sharepoint, el Portal de administración de Azure, etc.

![Resumen de uso de la aplicación](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### Uso de la aplicación: detallado

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Use este informe cuando quiera ver cuánto se está usando una aplicación SaaS específica. Este informe se basa en el número de veces que los usuarios hacen clic en la aplicación en el Panel de acceso. | Directorio > pestaña Informes |

### Panel de la aplicación

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Este informe indica los inicios de sesión acumulados en la aplicación por los usuarios de su organización en un intervalo de tiempo seleccionado. El gráfico de la página Panel le ayudará a identificar las tendencias de uso de la aplicación. | Directorio > Aplicación > pestaña Panel |

## Informes de errores

### Errores de aprovisionamiento de cuentas

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Úselo para supervisar los errores que se producen durante la sincronización de cuentas desde las aplicaciones SaaS a Azure Active Directory. | Directorio > pestaña Informes |

![Errores de aprovisionamiento de cuentas](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## Informes específicos del usuario

### Dispositivos

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Use este informe si desea ver la dirección IP y la ubicación geográfica de los dispositivos que un usuario específico ha usado para tener acceso a Active Directory de Azure. | Directorio > Usuarios > <i>Usuario</i> > pestaña Dispositivos |

### Actividad

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra la actividad de inicio de sesión de un usuario. El informe incluye información como la aplicación en la que se ha iniciado sesión, el dispositivo usado, la dirección IP y la ubicación. No recopilamos el historial de los usuarios que inician sesión con una cuenta de Microsoft. | Directorio > Usuarios > <i>Usuario</i> > pestaña Actividad |

#### Eventos de inicio de sesión incluidos en el informe de actividad

Solo determinados tipos de eventos de inicio de sesión aparecerán en el informe de actividad del usuario.

| Tipo de evento | ¿Se incluye? |
| ----------------------								| ---------		|
| Inicios de sesión en el [Panel de acceso](http://myapps.microsoft.com/) | Sí |
| Inicios de sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) | Sí |
| Inicios de sesión en el [Portal de Microsoft Azure](https://portal.azure.com/) | Sí |
| Inicios de sesión en el [portal de Office 365](http://portal.office.com/) | Sí |
| Inicios de sesión en una aplicación nativa, como Outlook (consulte la excepción a continuación) | Sí |
| Inicios de sesión en una aplicación federada/aprovisionada a través del Panel de acceso, como Salesforce | Sí |
| Inicios de sesión en una aplicación basada en contraseña a través del Panel de acceso, como Twitter | Sí |
| Inicios de sesión en una aplicación empresarial personalizada que se ha agregado al directorio | No (próximamente) |
| Inicios de sesión en una aplicación de Proxy de aplicación de Azure AD que se ha agregado al directorio | No (próximamente) |

> Nota: Para reducir la cantidad de ruido en este informe, no se muestran los inicios de sesión del [Asistente para el inicio de sesión de Microsoft Online Services](http://community.office365.com/es-ES/w/sso/534.aspx).










## Aspectos que se deben tener en cuenta si sospecha de una infracción de seguridad

Si sospecha que una cuenta de usuario puede estar en peligro o que cualquier tipo de actividad de usuario sospechosa puede dar lugar a una infracción de seguridad de los datos del directorio en la nube, considere la posibilidad de realizar una o varias de las acciones siguientes:

- Póngase en contacto con el usuario para comprobar la actividad.
- Restablezca la contraseña del usuario.
- [Habilite Multi-factor Authentication](multi-factor-authentication-get-started.md) para obtener seguridad adicional

## Visualización o descarga de un informe

1. En el Portal de administración de Azure, haga clic en **Active Directory**, haga clic en el nombre del directorio de la organización y, a continuación, haga clic en **Informes**.
2. En la página Informes, haga clic en el informe que desea ver o descargar.

    > [AZURE.NOTE] Si es la primera vez que se usa la característica de informes de Azure Active Directory, verá un mensaje para participar. Si está de acuerdo, haga clic en el icono de marca de verificación para continuar.

3. Haga clic en el menú desplegable situado al lado de Intervalo y, a continuación, seleccione uno de los siguientes intervalos de tiempo que se debe usar al generar este informe:
    - Últimas 24 horas
    - Últimos 7 días
    - Últimos 30 días
4. Haga clic en el icono de marca de verificación para ejecutar el informe.
	- Se mostrarán hasta 1000 eventos en el Portal de administración de Azure.
5. Si corresponde, haga clic en **Descargar** para descargar el informe en un archivo comprimido en formato de valores separados por comas (CSV) para verlo sin conexión o con fines de archivo.
	- Hasta 75000 eventos se incluirán en el archivo descargado.
	- Para ver más datos, consulte la [API de informes de Azure AD](active-directory-reporting-api-getting-started.md).

## Ignorar un evento

Si está viendo informes de anomalías, observará que puede ignorar varios eventos que se muestran en informes relacionados. Para ignorar un evento, resáltelo en el informe y, a continuación, haga clic en **Ignorar**. El botón **Ignorar** quitará de forma permanente el evento resaltado del informe y solo lo pueden usar administradores globales con licencia.

## Notificaciones de correo electrónico automáticas

Para obtener más información sobre las notificaciones de informes de Azure AD, consulte [Notificaciones de informes de Azure Active Directory](active-directory-reporting-notifications.md).

## Pasos siguientes

- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)

<!---HONumber=AcomDC_0128_2016-->