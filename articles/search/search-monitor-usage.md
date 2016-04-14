<properties 
   pageTitle="Supervisión del uso y estadísticas en un servicio Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
   description="Realice el seguimiento del consumo de recursos y el tamaño de índice para Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="02/04/2016"
   ms.author="heidist"/>

# Supervisión del uso y estadísticas en un servicio Búsqueda de Azure

Llevar un seguimiento del crecimiento de los índices y el tamaño de los documentos puede ayudarle a ajustar la capacidad de forma proactiva antes de alcanzar el límite superior que haya establecido para el servicio.

Para supervisar el uso de recursos, los recuentos y las estadísticas de su servicio pueden verse con facilidad en el [Portal de Azure](https://portal.azure.com), pero también se puede obtener la información mediante programación si va a crear una herramienta de administración de servicio personalizada. En este artículo se muestran los pasos para ambas técnicas.

También puede usar la nueva función de análisis de tráfico de búsqueda para obtener información sobre la actividad en el nivel de índice. Visite [Análisis de tráfico de búsqueda para Búsqueda de Azure](search-traffic-analytics.md) para comenzar.

##Ver recuentos y métricas en el portal 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 

2. Abra el panel del servicio Búsqueda de Azure. Puede buscar los mosaicos del servicio en la página principal, o puede desplazarse hasta el servicio usando Explorar, en la barra de salto. Consulte [Crear un servicio](search-create-service-portal.md) para obtener instrucciones paso a paso.

La sección Uso incluye un medidor que indica la parte de los recursos disponibles que está en uso.

  ![][1]

Recuerde que el servicio compartido tiene un máximo de una réplica y una partición. Además, sólo puede admitir 10.000 documentos en total o 50 MB de datos, lo que ocurra primero.

##Obtención de estadísticas de índice utilizando la API de REST

La API de REST de Búsqueda de Azure y el SDK para .NET proporcionan acceso mediante programación a las métricas del servicio. Si está utilizando [indizadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para cargar un índice desde una base de datos SQL de Azure o desde DocumentDB, está disponible una API adicional para obtener las cifras que necesita.

  + [Obtención de estadísticas de índice](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Recuento de documentos](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Obtención del estado del indizador](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## Pasos siguientes

Consulte [Límites y capacidad](search-limits-quotas-capacity.md) para determinar la combinación de particiones y réplicas que necesitará si la capacidad existente no es suficiente.

Visite [Administración del servicio de búsqueda en Microsoft Azure](search-manage.md) para obtener más información acerca de la administración de servicio.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 

<!---HONumber=AcomDC_0224_2016-->