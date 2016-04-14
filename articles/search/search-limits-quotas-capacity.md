<properties
	pageTitle="Límites de servicio en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Límites de servicio usados en la planeación de la capacidad y los límites máximos de solicitudes y respuestas de Búsqueda de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Límites de servicio en la Búsqueda de Azure

Los límites máximos del almacenamiento, las cargas de trabajo y las cantidades de índices, documentos y otros objetos dependen de si agrega Búsqueda de Azure a un plan de tarifa **Gratis**, **Básico** o **Estándar**.

- El plan **Gratis** es un servicio multiinquilino compartido incluido en su suscripción de Azure. Es una opción sin costo adicional para los suscriptores existentes que permite experimentar con el servicio antes de registrarse para obtener recursos dedicados. 
- El plan **Básico (vista preliminar)** proporciona recursos de proceso dedicados para cargas de trabajo de producción a escala más pequeña. Este plan está actualmente en vista preliminar y se ofrece a un [precio reducido](https://azure.microsoft.com/pricing/details/search/).
- El plan **Estándar** funciona en equipos dedicados que solo usa su servicio, con más capacidad de almacenamiento y de procesamiento en todos los niveles, incluida la configuración mínima. El plan Estándar se incluye en dos niveles (S1 y S2). 

## Límites de los niveles

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] Las consultas por segundo (QPS) son variables, especialmente en el servicio compartido, ya que el rendimiento se basa en ancho de banda disponible y la competición por los recursos del sistema. Los recursos de proceso y almacenamiento de Azure que atienden su servicio compartido se comparten entre varios suscriptores, por lo que el número de QPS de su solución variará en función de cuántas cargas de trabajo se ejecuten al mismo tiempo. En el nivel Estándar, puede calcular el número de QPS con más precisión porque tiene control sobre más parámetros. Consulte la sección de procedimientos recomendados en [Administrar la solución de búsqueda](search-manage.md) para obtener instrucciones sobre cómo calcular el número de QPS para las cargas de trabajo.

## Límites de la clave de API

Las claves de API se usan para la autenticación del servicio. Hay dos tipos. Las claves de administración se especifican en el encabezado de solicitud y conceden acceso completo de lectura y escritura al servicio. Las claves de consulta son de solo lectura, se especifican en la dirección URL y normalmente se distribuyen a las aplicaciones cliente.

- Máximo de 2 claves de administración por servicio
- Máximo de 50 claves de consultas por servicio

## Límites de solicitud

- Máximo de 16 MB por solicitud <sup>1</sup>
- Longitud máxima de dirección URL de 8 KB
- Máximo de 1000 documentos por lote del índice de cargas de índices, combinaciones o eliminaciones
- Máximo de 32 campos en cláusula $orderby
- El tamaño máximo del término de búsqueda es de 32 766 bytes (32 KB menos 2 bytes) de texto con codificación UTF-8

## Límites de respuestas

- Máximo de 1000 documentos devueltos por página de resultados de búsqueda
- Máximo de 100 sugerencias devueltas por solicitud de Sugerir API

<sup>1</sup> en Búsqueda de Azure, el cuerpo de una solicitud está sujeto a un límite superior de 16 megabytes, que impone un límite práctico en el contenido de campos individuales o colecciones que no esté restringido de algún modo por límites teóricos (consulte [Tipos de datos admitidos](https://msdn.microsoft.com/library/azure/dn798938.aspx) para más información sobre composición de campos y restricciones).

<!---HONumber=AcomDC_0302_2016-->