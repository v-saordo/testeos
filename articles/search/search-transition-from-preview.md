<properties 
	pageTitle="Transición de versión preliminar api-version=2014* a api-version=2015 * | Microsoft Azure | Servicio de búsqueda hospedado en la nube" 
	description="Obtenga información sobre cambios importantes y sobre cómo migrar el código escrito para 2014-07-31-preview o para 2014-10-20-preview a Búsqueda de Azure, api-version=2015-02-28." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/04/2016" 
	ms.author="heidist"/>

#Transición de versión preliminar api-version=2014* a api-version=2015 *#

Búsqueda de Azure es un servicio de búsqueda hospedado en la nube en Microsoft Azure. La siguiente orientación es para los clientes que crearon aplicaciones personalizadas en las versiones preliminares de Búsqueda de Azure y ahora están migrando a la versión disponible con carácter general, 2015-02-28.

Como cliente de la versión preliminar, puede usar cualquiera de estas versiones preliminares anteriores:

- 2014-07-31-Preview
- 2014-10-20-Preview

Ahora que Búsqueda de Azure está disponible con carácter general, recomendamos realizar la transición a versiones más recientes: 2015-02-28 es la versión oficial de la API de la versión de Búsqueda de Azure disponible con carácter general. Esta versión está documentada en [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx).

También estamos implementando la próxima versión preliminar, [2015-02-28-Preview](search-api-2015-02-28-preview.md), que introduce características que aún están en desarrollo. Puede proporcionar comentarios sobre la API preliminar a través de los [foros de Búsqueda de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch) o de nuestra [página de comentarios](https://feedback.azure.com/forums/263029-azure-search /).

###Lista de comprobación para la migración###

- Revise los cambios importantes para determinar si su solución se ve afectada.
- Cambie la versión de API a `2015-02-28` para la versión bloqueada. Esta versión responde a un Contrato de nivel de servicio (SLA). Si tiene problemas, los puede resolver más rápidamente.
- Compile, implemente, pruebe. Debe tener una paridad del 100% en términos de comportamientos de búsqueda, con la excepción de los cambios importantes.
- Impleméntela en producción.
- Evalúe las características nuevas para su futura adopción. Cambie de nuevo a la versión 2015-02-28-Preview si desea probar los procesadores de lenguaje natural de Microsoft o `morelikethis`.

##Cambios importantes en api-version=2015*##

La versión inicial de la API incluye una característica de sugerencias de autocompletar o escritura anticipada. Aunque útil, estaba limitado a coincidencias con prefijos y buscaba los primeros caracteres del término de búsqueda, sin compatibilidad para la coincidencia en otros lugares del campo. La implementación era una propiedad booleana denominada `suggestions` que se establecía en `true` cuando se deseaba habilitar la coincidencia con prefijos en un campo determinado.

Esta implementación original ahora está en desuso en favor de una nueva construcción `Suggesters` definida en la característica [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx), que proporciona coincidencia con infijos y coincidencia aproximada. Tal y como se desprende de los nombres, la coincidencia con infijos y aproximada proporciona una gama mucho más amplia de posibilidades de coincidencia. La coincidencia con infijos incluye los prefijos, ya que sigue coincidiendo con los caracteres del principio, pero amplía la búsqueda de coincidencias para incluir el resto de la cadena.

Hemos decidido interrumpir la implementación anterior (la propiedad booleana), lo que significa que deja de estar disponible por completo en cualquiera de las versiones de 2015; no se mantiene la compatibilidad con versiones anteriores para evitar su adopción involuntaria por parte de los clientes que creen soluciones nuevas. Si utiliza `2015-02-28` o `2015-02-28-Preview` deberá usar la nueva construcción `Suggesters` para habilitar las consultas con escritura anticipada.

##Importación del código existente##

La propiedad de sugerencia es el único cambio importante. Si no utiliza esta propiedad puede cambiar `api-version` desde `2014-07-31-Preview` o `2014-10-20-Preview` a `2015-02-28` y, a continuación, volver a compilar y a implementar. La aplicación funcionará como antes.

Las aplicaciones personalizadas que implementan sugerencias deben hacer lo siguiente:

1. Actualice todos los paquetes de NuGet.
1. Cambie api-version a `2015-02-28`. Si utiliza el ejemplo de código siguiente, api-version se encuentra en la clase **AzureSearchHelper**.
1. Elimine el atributo `Suggestions={true | false}` del esquema de JSON que define el índice.
1. Agregue una construcción al final del índice de `Suggesters` (como se muestra en la sección [Después](#after)).
1. Compruebe que puede publicar en el servicio (tendrá que cambiar el nombre del índice para evitar conflictos de nombres).
1. Recompile la solución e impleméntela en un entorno de prueba.
1. Ejecute todos los casos de prueba para asegurarse de que la solución se comporta según lo esperado.
1. Impleméntela en producción.

El ejemplo de código del [ejemplo de Adventure Works en codeplex](https://azuresearchadventureworksdemo.codeplex.com/) tiene la implementación original de `Suggestions`. Este ejemplo le puede servir para practicar la migración del código con el código de ejemplo.

En la siguiente sección, mostraremos un [antes](#before) y un [después](#after) de la implementación de sugerencias. Puede reemplazar el método **CreateCatalogIndex()** con la versión de la sección [después](#after) y, a continuación, compilar e implementar la solución para probar la nueva funcionalidad.

<a name="before"></a>
###Antes###

Como puede observar, `Suggestions` es una propiedad booleana que se define en cada campo. Elimine todos los atributos `Suggestions`.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###Después de###

Una definición de esquema migrada omite la propiedad `Suggestions`y agrega una construcción de `Suggesters` en la parte inferior.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##Evaluar los nuevos enfoques y las nuevas características##

Después de migrar la solución y comprobar que se ejecuta como esperaba, puede utilizar estos vínculos para obtener información acerca de las nuevas características.

- [Búsqueda de Azure está disponible con carácter general (entrada de blog)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Novedades en la actualización más reciente de Búsqueda de Azure](search-latest-updates.md)
- [¿Qué es Búsqueda de Azure?](search-what-is-azure-search.md)

##Obtener ayuda##

La versión `2015-02-28` de la API responde a un Contrato de nivel de servicio (SLA). Utilice las opciones de soporte técnico y los vínculos de [esta página](../support/options/) para presentar una incidencia de soporte técnico.

 

<!---HONumber=AcomDC_0224_2016-->