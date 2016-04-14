<properties
	pageTitle="Creación de un servicio Búsqueda de Azure en el Portal | Microsoft Azure | Servicio de búsqueda en la nube"
	description="Agregue un servicio Búsqueda de Azure gratis o estándar a una suscripción existente mediante el Portal de Azure. Búsqueda de Azure es un servicio de búsqueda hospedado en la nube para aplicaciones personalizadas."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="03/09/2016"
	ms.author="heidist"/>

# Creación de un servicio Búsqueda de Azure en el Portal de Azure

Búsqueda de Microsoft Azure es un servicio de búsqueda hospedado en la nube que le permite insertar funcionalidad de búsqueda en aplicaciones personalizadas. Proporciona un motor de búsqueda y almacenamiento para sus datos de búsqueda, a los que puede acceder y administrarlos mediante el Portal de Azure, un SDK de .NET o una API de REST. Entre las funciones clave se incluyen consultas de autocompletar, la coincidencia aproximada, la navegación con facetas, el resaltado de resultados, los perfiles de puntuación y la compatibilidad con varios idiomas. Para saber más del funcionamiento de Búsqueda de Azure, consulte [¿Qué es Búsqueda de Azure?](search-what-is-azure-search.md).

Búsqueda de Azure está disponible en los niveles de precios comprendidos entre gratis (compartido) y básico o estándar, donde el costo se prorratea en función de la capacidad para la que está suscrito.

## Incorporación de Búsqueda de Azure a una suscripción de forma gratuita

Como administrador, puede agregar Búsqueda de Azure a una suscripción de Azure existente sin costo alguno al seleccionar el servicio compartido. Para comenzar su evaluación, puede registrarse para obtener una [suscripción para una evaluación gratuita de Azure](../../includes/free-trial-note.md).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En la barra de salto, haga clic en **Nuevo** > **Datos + almacenamiento** > **Búsqueda**.

     ![][1]

3. Configure el nombre del servicio, el plan de tarifas, el grupo de recursos, la suscripción y la ubicación. Estos valores son necesarios y no se puede cambiar una vez que se aprovisiona el servicio.

     ![][2]

	- El **nombre de servicio** debe ser único, estar en minúscula, tener un máximo de 60 caracteres y no incluir espacios. Este nombre forma parte del punto de conexión del servicio de Búsqueda de Azure (por ejemplo, "https://**mi-nombre-servicio**.search.windows.net"). Consulte [Reglas de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para obtener más información acerca de las convenciones de nomenclatura.

	- **Nivel de precios** determina la capacidad y la facturación. Los dos niveles proporcionan las mismas características, pero con niveles de recursos distintos.

		- **Gratuito** se ejecuta en clústeres que se comparten con otros suscriptores. Ofrece suficiente capacidad para probar tutoriales y escribir código de prueba de concepto, pero no está destinado a aplicaciones de producción. Implementar un servicio gratuito suele llevar pocos minutos.
		- **Básico (versión preliminar)** se ejecuta en recursos dedicados pero con límites y precios inferiores para cargas de trabajo de producción más pequeñas. Se puede escalar hasta en 3 réplicas y 1 partición, suficiente para la alta disponibilidad necesaria para ejecución de consultas.
		- **Estándar** se ejecuta en recursos dedicados y es altamente escalable. Inicialmente, se aprovisiona un servicio estándar con una réplica y una partición, pero se puede aumentar la capacidad a un máximo de 36 unidades de búsqueda una vez creado el servicio. Implementar un servicio estándar lleva más tiempo, normalmente unos 15 minutos.

	- Los **grupos de recursos** son contenedores para servicios y recursos que se usan para un objetivo común. Por ejemplo, si va a compilar una aplicación de búsqueda personalizada basada en Búsqueda de Azure, la función Aplicaciones web en Servicio de aplicaciones de Azure y el almacenamiento de blobs de Azure, puede crear un grupo de recursos que mantenga estos servicios juntos en las páginas de administración del portal.

	- **Suscripción** permite elegir entre varias suscripciones, si tiene más de una.

	- **Ubicación** es la región del centro de datos. Actualmente, todos los recursos se deben ejecutar en el mismo centro de datos. No se admite la distribución de recursos entre varios centros de datos.

4. Haga clic en **Crear** para realizar el aprovisionamiento del servicio.

Preste atención a las notificaciones de la barra de salto. Cuando el servicio esté listo para su uso, aparecerá un aviso.

<a id="sub-3"></a>
## Incorporación de un servicio de búsqueda de nivel Básico o Estándar para obtener recursos dedicados

Muchos clientes comienzan con el servicio gratuito y después cambian al nivel Básico o Estándar para dar cabida a cargas de trabajo mayores. Los niveles Básico y Estándar ofrecen recursos dedicados en un centro de datos de Azure que solo usted puede usar.

Las operaciones de Búsqueda de Azure requieren réplicas de almacenamiento y servicio. A diferencia del servicio gratis, que no tiene ninguna opción para agregar recursos, el nivel Estándar permite escalar verticalmente para agregar más almacenamiento o soporte de consultas al incrementar el recurso que sea más importante en sus cargas de trabajo. El nivel Básico también le permite escalar verticalmente, pero solo para las réplicas, con un límite máximo de tres.

Para usar el nivel Básico o Estándar, debe crear un servicio de búsqueda con ese nivel de precios. Puede repetir los pasos anteriores de este artículo para crear un servicio Búsqueda de Azure. Tenga en cuenta que la configuración de recursos dedicados puede tardar unos minutos (hasta 15 minutos o más).

No hay ninguna actualización de la versión gratuita. El cambio de niveles, con el potencial de escalación, requiere un nuevo servicio. Tendrá que volver a cargar los índices y documentos usados por su aplicación de búsqueda.

El servicio Búsqueda de Azure a nivel Básico o Estándar se crea con una réplica y una partición, pero se puede escalar fácilmente a niveles de recursos más altos.

1.	Después de que se haya creado el servicio, vuelva al panel del servicio.

2.	Haga clic en el icono **Escalar**.

3.	Utilice los controles deslizantes para agregar réplicas, particiones o ambos para el nivel Estándar. En cuanto al nivel Básico, puede aumentar las réplicas hasta un máximo de tres.

Las réplicas y las particiones adicionales se cobran en unidades de búsqueda. El total de unidades de búsqueda necesario para admitir una configuración de recursos en particular se muestra en la página, a medida que agrega recursos.

Puede consultar [Detalles de precios](http://go.microsoft.com/fwlink/p/?LinkID=509792) para obtener la información de facturación por unidad. Consulte [Capacity planning](search-capacity-planning.md) (Planificación de capacidad) para obtener ayuda para elegir las combinaciones de partición y de réplica.

<a id="sub-2"></a>
## Buscar el nombre del servicio y las claves de API del servicio Búsqueda de Azure

Después de crear el servicio, puede volver al Portal de Azure para obtener la dirección URL o la `api-key`. Las conexiones con el servicio de Búsqueda de Azure requieren que tenga la URL y una `api-key` para autenticar la llamada.

1. En la barra de salto, haga clic en **Inicio** y, a continuación, haga clic en el servicio Búsqueda de Azure para abrir el panel del servicio.

2. En el panel del servicio verá mosaicos con información esencial, así como el icono de llave para tener acceso a las claves de administrador.

  	![][3]

3. Copie la dirección URL del servicio y una clave de administración. Los necesitará para la tarea siguiente, [Prueba de disponibilidad del servicio](#sub-4).


<a id="sub-4"></a>
## Prueba de disponibilidad del servicio

La confirmación de que su servicio está operativo y que puede obtener acceso a él desde una aplicación cliente es el paso final en la configuración de la Búsqueda de Azure. Puede usar [Fiddler con Búsqueda de Azure](search-fiddler.md) para comprobar la disponibilidad del servicio.

<!--Next steps and links -->
<a id="next-steps"></a>
## Pasos siguientes

Ahora que se ha creado el servicio, puede realizar los pasos siguientes: crear un [índice](search-what-is-an-index.md), [consultar un índice](search-query-overview.md), crear y administrar aplicaciones de búsqueda que usan Búsqueda de Azure.

- [Creación de un índice de Búsqueda de Azure en el Portal de Azure](search-create-index-portal.md)

- [Consulta de un índice de Búsqueda de Azure mediante el Explorador de búsqueda en el Portal de Azure](search-explorer.md)

- [Cómo usar la Búsqueda de Azure en .NET](search-howto-dotnet-sdk.md)

- [Administración de la solución de búsqueda en Microsoft Azure](search-manage.md)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=AcomDC_0309_2016-->