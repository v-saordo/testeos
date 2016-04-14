<properties
   pageTitle="Cómo conservar una dirección IP virtual constante para un servicio en la nube | Microsoft Azure"
   description="Obtenga información para garantizar que no cambia la dirección IP virtual (VIP) de su servicio en la nube de Azure."
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
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Cómo conservar una dirección IP virtual constante para un servicio en la nube

Al actualizar un servicio en la nube que se hospeda en Azure, es posible que deba asegurarse de que no cambia la dirección IP virtual (VIP) del servicio. Muchos de los servicios de administración de dominio usan el Sistema de nombres de dominio (DNS) para registrar nombres de dominio. DNS solo funciona si la dirección VIP sigue siendo la misma. Puede usar el **Asistente para publicación** en Azure Tools para asegurarse de que la dirección VIP del servicio en la nube no cambia cuando la actualiza. Para obtener más información sobre cómo usar la administración de dominios DNS para servicios en la nube, vea [Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure](/cloud-services/cloud-services-custom-domain-name.md).

## Publicar un servicio en la nube sin cambiar su VIP

La dirección VIP de un servicio en la nube se asigna al implementarla por primera vez en Azure en un entorno determinado, como el entorno de producción. La dirección VIP no cambia a menos que elimine la implementación de manera explícita o que se elimine implícitamente por el proceso de actualización de implementación. Para conservar la dirección VIP, no debe eliminar su implementación y también debe asegurarse de que Visual Studio no elimina su implementación automáticamente. Puede controlar el comportamiento mediante la especificación de la configuración de implementación en el **Asistente para publicación**, que admite varias opciones de implementación. Puede especificar una nueva implementación o una implementación de actualización, que puede ser incremental o simultánea, y ambos tipos de implementaciones de actualización conservan la dirección VIP. Para obtener definiciones de estos tipos diferentes de implementación, vea [Asistente Publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md). Además, puede controlar si la implementación anterior de un servicio en la nube se elimina si se produce un error. La dirección VIP podría cambiar de forma inesperada si esa opción no se establece correctamente.

### Para actualizar un servicio en la nube sin cambiar su VIP

1. Cuando haya implementado el servicio en la nube al menos una vez, abra el menú contextual para su proyecto de Azure y luego elija **Publicar**. Aparecerá el asistente **Publicar aplicación de Azure**.

1. En la lista de suscripciones, elija la que quiere implementar y luego el botón **Siguiente**. Aparecerá la página **Configuración** del asistente.

1. En la pestaña **Configuración común**, compruebe que el nombre del servicio en la nube en el que está implementando, el **Entorno**, la **Configuración de compilación** y el **Configuración del servicio** son todos correctos.

1. En la pestaña **Configuración avanzada**, compruebe que la cuenta de almacenamiento y la etiqueta de implementación son correctas, que la casilla **Eliminar implementación en caso de error** está desactivada y que la casilla de actualización **Implementación** está activada. Seleccione la casilla de verificación **Implementación** para asegurarse de que no se eliminará la implementación y de que no se perderá la dirección VIP al volver a publicar la aplicación. Desactive la casilla **Eliminar implementación en caso de error** para asegurarse de que no se perderá la dirección VIP si se produce un error durante la implementación.

1. Para especificar más cómo quiere que se actualicen los roles, elija el vínculo **Configuración** junto al cuadro **Actualización de implementación** y elija la opción de actualización simultánea o actualización incremental en el cuadro de diálogo de configuración **Actualización de implementación**. Si elige la actualización incremental, se actualizará cada instancia una tras otra, para que la aplicación esté siempre disponible. Si elige la actualización simultánea, se actualizarán todas las instancias al mismo tiempo. La actualización simultánea es más rápida, pero es posible que el servicio no esté disponible durante el proceso de actualización.

1. Cuando esté satisfecho con la configuración, elija el botón **Siguiente**.

1. En la página **Resumen** del asistente, compruebe la configuración y luego elija el botón **Publicar**.

  >[AZURE.WARNING] Si se produce un error en la implementación, debe tratar por qué se produjo el error y volver a implementar rápidamente, para evitar dejar el servicio en la nube en un estado dañado.

## Pasos siguientes

Para obtener información sobre la publicación en Azure desde Visual Studio, consulte [Asistente Publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).

<!---HONumber=AcomDC_0204_2016-->