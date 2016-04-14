<properties
   pageTitle="Configuración de un proyecto de servicio en la nube de Azure con Visual Studio | Microsoft Azure"
   description="Aprenda a configurar un proyecto de servicio en la nube de Azure en Visual Studio dependiendo de los requisitos para dicho proyecto."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/05/2016"
   ms.author="tarcher" />

# Configuración de un proyecto de servicio en la nube de Azure con Visual Studio.

Es posible configurar un proyecto de servicio en la nube de Azure dependiendo de los requisitos para dicho proyecto. Puede establecer las propiedades del proyecto para las siguientes categorías:

- **Publicación de un servicio en la nube en Azure**

  Puede establecer una propiedad para asegurarse de que no se elimina accidentalmente un servicio en la nube existente implementado en Azure.

- **Ejecución o depuración de un servicio en la nube en el equipo local**

  Puede seleccionar una configuración del servicio para usarla e indicar si desea iniciar el emulador de almacenamiento de Azure.

- **Validación de un paquete de servicios en la nube cuando se crea**

  Puede decidir si desea tratar las advertencias como errores para asegurarse de que el paquete de servicio en la nube se va a implementar sin ningún problema. Esto reduce el tiempo de espera si implementa y, a continuación, comprueba que se ha producido un error.

La ilustración siguiente muestra cómo seleccionar una configuración para usarla al ejecutar o depurar el servicio en la nube de manera local. Puede establecer cualquiera de las propiedades del proyecto que necesite en esta ventana, tal como se muestra en la ilustración.

![Configuración de un proyecto de Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## Para configurar un proyecto de servicio en la nube de Azure

1. Para configurar un proyecto de servicio en la nube desde el **Explorador de soluciones**, abra el menú contextual del proyecto del servicio en la nube y luego elija **Propiedades**.

  Se mostrará una página con el nombre del proyecto del servicio en la nube en el editor de Visual Studio.

1. Elija la pestaña **Desarrollo**.

1. Para asegurarse de que no elimina accidentalmente una implementación existente de Azure, en el mensaje de confirmación que se muestra antes de eliminar una lista de distribución existente, elija **True**.

1. Para seleccionar la configuración del servicio que se va a usar al ejecutar o depurar el servicio en la nube de manera local, elija la configuración del servicio en la lista **Configuración del servicio**.

  >[AZURE.NOTE]Si desea crear una configuración de servicio para usarla, vea Administración de configuraciones del servicio y perfiles. Si desea modificar una configuración de servicio para un rol, vea [Configuración de los roles para un servicio en la nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Para iniciar el emulador de almacenamiento de Azure al ejecutar o depurar el servicio en la nube de manera local, en **Iniciar emulador de almacenamiento de Azure**, elija **True**.

1. Para asegurarse de que no se puede publicar si hay errores de validación del paquete, en **Tratar advertencias como errores**, elija **True**.

1. Para asegurarse de que el rol web usa el mismo puerto cada vez que se inicia de manera local en IIS Express, en **Usar puertos de proyecto web**, elija **True**. Para usar un puerto específico para un proyecto web en particular, abra el menú contextual del proyecto web, elija la pestaña **Propiedades**, elija la pestaña **Web** y cambie el número de puerto en la configuración **Dirección Url del proyecto** en la sección **IIS Express**. Por ejemplo, escriba `http://localhost:14020` como dirección URL del proyecto.

1. Para guardar los cambios realizados en las propiedades del proyecto de servicio en la nube, elija el botón **Guardar** en la barra de herramientas.

## Pasos siguientes

Para obtener más información sobre de cómo configurar los proyectos de servicios en la nube de Azure en Visual Studio, vea [Configuración del proyecto de Azure con varias configuraciones de servicio](vs-azure-tools-multiple-services-project-configurations.md).

<!---HONumber=AcomDC_0107_2016-->