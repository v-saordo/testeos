<properties
	pageTitle="Administración de un área de trabajo de aprendizaje automático | Microsoft Azure"
	description="Administrar el acceso a las áreas de trabajo del aprendizaje automático de Azure, e implementar y administrar servicios web de la API del aprendizaje automático"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="garye"/>


# Administración de un área de trabajo de Aprendizaje automático de Azure
Mediante el Portal de Azure clásico, puede administrar las áreas de trabajo de Aprendizaje automático para realizar las siguientes tareas:

- Supervisar el uso del área de trabajo
- Configurar el área de trabajo para permitir o denegar el acceso
- Administrar servicios web creados en el área de trabajo
- Eliminar el área de trabajo

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Además, la pestaña Panel muestra una descripción general del uso del área de trabajo y una vista rápida de la información que contiene.

> [AZURE.TIP] En Estudio de aprendizaje automático de Azure, en la pestaña **Servicios web**, puede agregar, actualizar o eliminar un servicio web de Aprendizaje automático.

Para administrar un área de trabajo:

1.	Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con su cuenta de Microsoft Azure (use la cuenta que está asociada a la suscripción de Azure).
2.	En el panel de servicios de Microsoft Azure, haga clic en **APRENDIZAJE AUTOMÁTICO**.
3.	Haga clic en el área de trabajo que desea administrar.

La página del área de trabajo tiene tres pestañas:

- **Panel**: permite ver el uso y la información del área de trabajo.
- **Configurar**: permite administrar el acceso al área de trabajo.
- **Servicios web**: permite administrar los servicios web que se han publicado desde esta área de trabajo.


## Para supervisar el uso del área de trabajo

Haga clic en la pestaña **Panel**.

En el panel, puede ver el uso general del área de trabajo y obtener una vista rápida de su información.

- El gráfico **Proceso** muestra los recursos de proceso que se usan en el área de trabajo. Puede cambiar la vista para mostrar valores relativos o absolutos, y puede cambiar el período de tiempo que se muestra en el gráfico.
- **Información general del uso**: muestra el almacenamiento de Azure que usa el área de trabajo.
- **Vista rápida**: proporciona un resumen de la información del área de trabajo y vínculos útiles.

> [AZURE.NOTE] El vínculo **Iniciar sesión en estudio de aprendizaje automático** permite abrir Estudio de aprendizaje automático mediante la cuenta Microsoft con la que haya iniciado la sesión actual. La cuenta de Microsoft que usó para iniciar sesión en el Portal de Azure clásico para crear un área de trabajo no tiene automáticamente permiso para abrir el área de trabajo. Para abrir un área de trabajo, debe iniciar sesión en la cuenta de Microsoft que se definió como propietaria del área de trabajo. También puede hacerlo si recibe una invitación del propietario para unirse al área de trabajo.


## Concesión o suspensión del acceso de los usuarios ##

Haga clic en la pestaña **Configurar**.

Desde la pestaña de configuración, puede realizar las siguientes acciones:

- Suspender el acceso al área de trabajo de Aprendizaje automático haciendo clic en Denegar. Los usuarios ya no podrán abrir el área de trabajo en Estudio de aprendizaje automático. Para restaurar el acceso, haga clic en Permitir.
- Cambiar el propietario del área de trabajo especificando otra cuenta de Microsoft.

Para administrar cuentas adicionales que dispongan de acceso al área de trabajo en Estudio de aprendizaje automático, haga clic en **Iniciar sesión en estudio de aprendizaje automático** en la pestaña **PANEL** (consulte la nota anterior respecto a **Iniciar sesión en estudio de aprendizaje automático**). De esta manera, se abre el área de trabajo en Estudio de aprendizaje automático. Desde aquí, haga clic en la pestaña **Configuración** y, a continuación, en **Usuarios**. Puede hacer clic en **Invitar más usuarios** para proporcionar acceso a otros usuarios al área de trabajo. También puede seleccionar un usuario y hacer clic en **Quitar**.


## Para administrar servicios web en esta área de trabajo

Haga clic en la ficha **Servicios web**.

Esto muestra una lista de servicios web publicados desde esta área de trabajo. Para administrar un servicio web, haga clic en el nombre en la lista para abrir la página del servicio web.

Un servicio web puede tener uno o varios extremos definidos.

- Puede definir más puntos de conexión, además del punto de conexión "predeterminado". Para agregar un extremo, haga clic en **Agregar extremo** en la parte inferior de la página.

- Para eliminar un extremo (no se puede eliminar el extremo "predeterminado"), haga clic en cualquier parte en la fila del extremo (excepto en el nombre) y haga clic en **Eliminar extremo** en la parte inferior de la página. De esta manera, se elimina el extremo del servicio web.

    > [AZURE.NOTE] Si una aplicación está utilizando el extremo del servicio web cuando se elimina dicho extremo, la aplicación recibirá un error la próxima vez que intente acceder al servicio.

Haga clic en el nombre de un extremo de servicio web para abrirlo. El gráfico de uso muestra los recursos de proceso y de predicción que está utilizando el extremo del servicio web. Puede cambiar la vista para mostrar valores relativos o absolutos, y puede cambiar el período de tiempo que se muestra en el gráfico.

Esta página también proporciona la información que necesita para acceder al extremo mediante la API de REST del servicio web. Para obtener más información, consulte [Consumo de servicios web de Aprendizaje automático de Azure][consume].

También puede publicar el servicio web en el mercado de datos de Azure desde esta página. Para obtener más información, consulte [Publicación de los servicios web de Aprendizaje automático de Azure en Azure Marketplace][marketplace].

Haga clic en la pestaña **CONFIGURAR** para modificar la descripción, controlar el número de conexiones simultáneas que permite que el servicio web o configurar un seguimiento de diagnóstico.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

<!---HONumber=AcomDC_0204_2016-->