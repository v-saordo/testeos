<properties
   pageTitle="Introducción a los informes de Azure Active Directory | Microsoft Azure"
   description="Enumera los distintos informes disponibles en los informes de Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/05/2016"
   ms.author="curtand;kenhoff"/>

# Introducción a los informes de Azure Active Directory

## ¿Qué es?

Azure Active Directory (Azure AD) incluye informes de seguridad, actividad y auditoría para el directorio. Esta es una lista de los informes incluidos:

### Informes de seguridad

- Inicios de sesión desde orígenes desconocidos
- Inicios de sesión tras varios errores
- Inicios de sesión desde varias ubicaciones geográficas
- Inicios de sesión desde direcciones IP con actividad sospechosa
- Actividad de inicio de sesión irregular
- Inicios de sesión desde dispositivos posiblemente infectados
- Usuarios con actividad de inicio de sesión erróneo.

### Informes de actividad

- Uso de la aplicación: resumen
- Uso de la aplicación: detallado
- Panel de la aplicación
- Errores de aprovisionamiento de cuentas
- Dispositivos de usuario individuales
- Actividad de usuario individual
- Informe de actividad de grupos
- Informe de actividad de registro de restablecimiento de contraseña
- Actividad de restablecimiento de contraseña

### Informes de auditoría

- Informe de auditoría de directorio

> [AZURE.TIP]Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).



## Cómo funciona


### Canalización de informes

La canalización de informes consta de tres pasos principales. Cada vez que un usuario inicia sesión o se realiza una autenticación, ocurre lo siguiente:

- En primer lugar, el usuario se autentica (correctamente o no) y el resultado se almacena en las bases de datos de servicio de Azure Active Directory.
- En intervalos regulares, se procesan todos los inicios de sesión recientes. En este momento, la seguridad y los algoritmos de actividades anómalas buscan todos los inicios de sesión recientes en busca de actividad sospechosa.
- Después del procesamiento, los informes se escriben, se almacenan en caché y se ofrecen en el Portal de Azure clásico.

### Tiempos de generación de informes

Debido al gran volumen de autenticaciones e inicios de sesión procesados por la plataforma de Azure AD, los inicios de sesión más recientes tienen una antigüedad media de una hora. En casos excepcionales, se puede tardar hasta 8 horas en procesar los inicios de sesión más recientes.

Puede encontrar el inicio de sesión procesado más reciente examinando el texto de ayuda en la parte superior de cada informe.

![Texto de ayuda en la parte superior de cada informe](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP]Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).



## Introducción


### Iniciar sesión en el Portal de Azure clásico

En primer lugar, tendrá que iniciar sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) como administrador global o de cumplimiento. También debe ser administrador de servicios de suscripción de Azure o coadministrador, o usar la suscripción de Azure de acceso a Azure AD.

### Ir a los informes

Para ver los informes, diríjase a la pestaña Informes en la parte superior del directorio.

Si es la primera vez que visualiza los informes, deberá aceptar un cuadro de diálogo para poder verlos. Esto es para garantizar que los administradores de la organización permiten ver estos datos, que pueden considerarse información privada en algunos países.

![Cuadro de diálogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### Exploración de cada informe

Vaya a cada informe para ver los datos recopilados y los inicios de sesión procesados. Puede encontrar una [lista de todos los informes aquí](active-directory-reporting-what-it-is.md).

![Todos los informes](./media/active-directory-reporting-getting-started/reportsMain.png)

### Descarga de los informes como CSV

Cada informe se puede descargar como archivo CSV (valores separados por comas). Puede utilizar estos archivos en Excel, PowerBI o programas de análisis de terceros para seguir analizando los datos.

Para descargar cualquier informe como CSV, vaya hasta el informe y haga clic en "Descargar" en la parte inferior.

![Botón Descargar](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP]Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).





## Pasos siguientes

### Personalización de las alertas para la actividad de inicio de sesión anómala

Vaya a la pestaña "Configurar" del directorio.

Desplácese a la sección "Notificaciones".

Habilite o deshabilite la sección "Notificaciones de correo electrónico de inicios de sesión anómalos".

![Sección "Notificaciones"](./media/active-directory-reporting-getting-started/notificationsSection.png)

### Integración con la API de informes de Azure AD

Consulte [Introducción a la API de informes](active-directory-reporting-api-getting-started.md).

### Uso de Multi-Factor Authentication en usuarios

Seleccione un usuario en un informe.

Haga clic en el botón "Habilitar MFA" en la parte inferior de la pantalla.

![Botón Multi-Factor Authentication en la parte inferior de la pantalla](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP]Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).




## Más información


### Eventos de auditoría

Obtenga información acerca de qué eventos se auditan en el directorio en [Eventos de auditoría de informes de Azure Active Directory](active-directory-reporting-audit-events.md).

### Integración de la API

Consulte [Introducción a la API de informes](active-directory-reporting-api-getting-started.md) y la [documentación de referencia de la API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### Ponerse en contacto

Envíe un correo electrónico a [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) para trasmitir sus comentarios, solicitar ayuda o plantear las preguntas que tenga.

> [AZURE.TIP]Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).

<!---HONumber=AcomDC_0107_2016-->