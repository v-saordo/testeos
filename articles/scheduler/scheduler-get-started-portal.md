<properties
 pageTitle="Introducción al Programador de Azure en el Portal de Azure | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="02/17/2016"
 ms.author="krisragh"/>

# Introducción al Programador de Azure en el Portal de Azure

Es fácil crear trabajos programados en el Programador de Azure. En este tutorial, aprenderá a crear un trabajo: También aprenderá las funcionalidades de supervisión y administración del Programador.

## Creación de un trabajo

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com/).  

2.  Haga clic en **+Nuevo** > escriba _Programador_ en el cuadro de búsqueda > seleccione **Programador** en resultados > haga clic en **Crear**.

   ![][marketplace-create]

3.  Vamos a crear un trabajo que simplemente selecciona http://www.microsoft.com/ con una solicitud GET. En la pantalla **Trabajo del Programador**, escriba la siguiente información:

    1.  **Nombre:** `getmicrosoft`  

    2.  **Suscripción**: su suscripción a Azure.

    3.  **Colección de trabajos:** seleccione una colección de trabajos existente o haga clic en **Crear nueva** > escriba un nombre.

4.  Después, en **Configuración de la acción**, defina los siguientes valores:

    1.  **Tipo de acción:** ` HTTP`  

    2.  **Método:** `GET`

    3.  **URL:** ` http://www.microsoft.com`

   ![][action-settings]

5.  Por último, vamos a definir una programación. El trabajo se puede definir como un trabajo único, pero vamos a seleccionar una programación de periodicidad:

    1. **Periodicidad**: `Recurring`

    2. **Inicio**: la fecha de hoy

    3. **Repetir cada:** `12 Hours`

    4. **Finalización**: dos días a partir de hoy

   ![][recurrence-schedule]

6.  Haga clic en **Crear**

## Administración y supervisión de trabajos

Una vez que se crea un trabajo, aparece en el Panel principal de Azure. Haga clic en el trabajo y se abrirá una nueva ventana con las pestañas siguientes:

1.  Propiedades  

2.  Configuración de la acción

3.  Schedule

4.  Historial

5.  Usuarios

   ![][job-overview]

### Propiedades

Estas propiedades de solo lectura describen los metadatos de administración para el trabajo del Programador.

   ![][job-properties]


### Configuración de la acción

Al hacer clic en un trabajo en la pantalla **Trabajos** puede configurar ese trabajo. Esto le permitirá configurar opciones avanzadas, si no las ha configurado en el Asistente de creación rápida.

Para todos los tipos de acción, puede cambiar la directiva de reintentos y la acción en caso de error.

Para los tipos de acción de los trabajos HTTP y HTTPS, puede cambiar el método a cualquier verbo HTTP permitido. También puede agregar, eliminar o cambiar los encabezados y la información de autenticación básica.

Para los tipos de acciones de la cola de almacenamiento, puede cambiar la cuenta de almacenamiento, el nombre de la cola, el token SAS y el cuerpo.

Para los tipos de acción del Bus de servicio, puede cambiar el espacio de nombres, la ruta de tema/cola, la configuración de autenticación, el tipo de transporte, las propiedades de mensaje y el cuerpo del mensaje.

   ![][job-action-settings]

### Schedule

Esto le permite volver a configurar la programación, si es que desea cambiar la programación que creó en el Asistente de creación rápida.

Esta es una oportunidad para crear [programaciones complejas y periodicidad avanzada en el trabajo](scheduler-advanced-complexity.md)

Puede cambiar la fecha y hora de inicio, la programación de periodicidad y la fecha y hora de finalización (si el trabajo es periódico).

   ![][job-schedule]


### Historial

La pestaña **Historial** muestra las métricas seleccionadas para cada ejecución del trabajo en el sistema para el trabajo seleccionado. Estas métricas proporcionan valores en tiempo real relacionados con el estado del Programador:

1.  Status  

2.  Detalles

3.  Número de reintentos

4.  Periodicidad: 1ª, 2ª, 3ª, etc..

5.  Hora de inicio de ejecución

6.  Hora de finalización de ejecución

   ![][job-history]

Puede hacer clic en una ejecución para ver los **Detalles del historial** incluida la respuesta completa a cada ejecución. Este cuadro de diálogo también le permite copiar la respuesta en el Portapapeles.

   ![][job-history-details]

### Usuarios

El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso al Programador de Azure. Para aprender a utilizar la pestaña de usuarios, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)


## Consulte también

 [¿Qué es Programador?](scheduler-intro.md)

 [Jerarquía de entidades, terminología y conceptos del Programador](scheduler-concepts-terms.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Creación de programaciones complejas y periodicidad avanzada con Programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST del Programador](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell del Programador](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad del Programador](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error del Programador](scheduler-limits-defaults-errors.md)

 [Autenticación de salida del Programador](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_0218_2016-->