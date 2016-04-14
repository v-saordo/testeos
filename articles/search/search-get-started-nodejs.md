<properties
	pageTitle="Introducción a Búsqueda de Azure en NodeJS | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Revise la creación de una aplicación de búsqueda en un servicio de búsqueda hospedado en la nube en Azure con NodeJS como lenguaje de programación."
	services="search"
	documentationCenter=""
	authors="EvanBoyle"
	manager="pablocas"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="03/08/2016"
	ms.author="evboyle"/>

# Introducción a Búsqueda de Azure en NodeJS
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Aprenda a crear una aplicación de búsqueda de NodeJS personalizada que utiliza Búsqueda de Azure para la experiencia de búsqueda. Este tutorial usa la [API de REST del servicio Búsqueda de Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir los objetos y las operaciones que se utilizan en este ejercicio.

Hemos utilizado [NodeJS](https://nodejs.org) y NPM, [Sublime Text 3](http://www.sublimetext.com/3) y Windows PowerShell en Windows 8.1 para desarrollar y probar este código.

Para ejecutar este ejemplo, debe tener un servicio Búsqueda de Azure, al que puede suscribirse en el [Portal de Azure](https://portal.azure.com). Para obtener instrucciones detalladas, consulte [Creación de un servicio Búsqueda de Azure en el Portal de Azure](search-create-service-portal.md).

## Acerca de los datos

Esta aplicación de ejemplo usa los datos del [Servicio geológico de Estados Unidos (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados por el estado de Rhode Island para reducir el tamaño del conjunto de datos. Vamos a usar estos datos para crear una aplicación de búsqueda que devuelva edificios de referencia, como hospitales y escuelas, además de características geológicas como ríos, lagos y montes.

En esta aplicación, el programa **DataIndexer** compila y carga el índice usando una construcción [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), y obtiene el conjunto de datos filtrado de USGS desde una base de datos SQL pública de Azure. En el código del programa se proporcionan las credenciales y la información de conexión al origen de datos en línea. No es necesario realizar ninguna otra configuración.

> [AZURE.NOTE] Se aplicó un filtro a este conjunto de datos para no sobrepasar el límite de 10.000 documentos del nivel de precios gratuito. Si usa el nivel estándar, este límite no se aplica. Para obtener información más detallada sobre las características de cada plan de tarifa, consulte [Límites de servicio en la Búsqueda de Azure](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## Buscar el nombre del servicio y la clave de API del servicio Búsqueda de Azure

Después de crear el servicio, vuelva al portal para obtener la dirección URL o la `api-key`. Las conexiones con el servicio de búsqueda requieren que tenga la URL y una `api-key` para autenticar la llamada.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra de acceso rápido, haga clic en **Servicio de búsqueda** para enumerar todos los servicios de Búsqueda de Azure aprovisionados para la suscripción.
3. Seleccione el servicio que desea utilizar.
4. En el panel del servicio verá mosaicos con información esencial, así como el icono de llave para tener acceso a las claves de administrador.

  	![][3]

5. Copie la dirección URL del servicio, una clave de administración y una clave de consulta. Necesitará las tres más adelante para agregarlas al archivo config.js.

## Descarga de los archivos de ejemplo

Use uno de los siguientes métodos para descargar el ejemplo.

1. Vaya a [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).
2. Haga clic en **Download ZIP**, guarde el archivo .zip y extraiga todos los archivos que contiene.

Todas las modificaciones y las instrucciones de ejecución subsiguientes se realizarán en los archivos de esta carpeta.

Como alternativa, si tiene GIT en la instrucción de ruta de acceso, abra una ventana de PowerShell y escriba `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git`

## Actualización del archivo config.js. con la dirección URL del servicio de búsqueda y la clave de API

Utilizando la dirección URL y la api-key que copió anteriormente, especifique la dirección URL, la clave de administración y la clave de consulta en el archivo de configuración.

La clave de administración concede control total sobre las operaciones de servicio, incluidas la creación o eliminación de un índice y la carga de documentos. En cambio, las claves de consulta son para operaciones de solo lectura, utilizadas normalmente por las aplicaciones cliente que se conectan a Búsqueda de Azure.

En este ejemplo, se incluye la clave de consulta para ayudar a reforzar la práctica recomendada de usar la clave de consulta en aplicaciones cliente.

La siguiente captura de pantalla muestra el archivo **config.js** abierto en un editor de texto, con las entradas pertinentes enmarcadas para que pueda ver dónde actualizar el archivo con los valores válidos para el servicio de búsqueda.

![][5]


## Hospedar un entorno de tiempo de ejecución para el ejemplo

El ejemplo requiere un servidor HTTP, que puede instalar globalmente mediante npm.

Ejecute los comandos siguientes en una ventana de PowerShell.

1. Navegue hasta la carpeta que contiene el archivo **package.json**.
2. Escriba `npm install`.
2. Escriba `npm install -g http-server`.

## Compilación del índice y ejecución de la aplicación

1. Escriba `npm run indexDocuments`.
2. Escriba `npm run build`.
3. Escriba `npm run start_server`.
4. Dirija el explorador a `http://localhost:8080/index.html`

## Buscar en los datos de USGS

El conjunto de datos de USGS incluye los registros que son relevantes para el estado de Rhode Island. Si hace clic en **Search** en un cuadro de búsqueda vacío, obtendrá las 50 primeras entradas (es el valor predeterminado).

Escriba un término de búsqueda para que el motor de búsqueda tenga con qué trabajar. Pruebe a escribir un nombre regional. "Roger Williams" fue el primer gobernador de Rhode Island. Hay numerosos parques, edificios y escuelas que llevan su nombre.

![][9]

También puede probar con alguno de estos términos:

- Pawtucket
- Pembroke
- goose +cape


## Pasos siguientes

Este es el primer tutorial de Búsqueda de Azure basado en NodeJS y en el conjunto de datos de USGS. Con el tiempo, ampliaremos este tutorial para mostrar otras características de búsqueda que podría querer usar en sus soluciones personalizadas.

Si ya tiene conocimientos sobre Búsqueda de Azure, puede usar este ejemplo como punto de partida para probar proveedores de sugerencias (escritura automática o autocompletar consultas), filtros y navegación por facetas. También puede mejorar la página de resultados de búsqueda si agrega recuentos y procesamiento por lotes de documentos para que los usuarios puedan navegar por las páginas de resultados.

¿Es la primera vez que usa Búsqueda de Azure? Le recomendamos que pruebe otros tutoriales para comprender mejor lo que puede crear. Visite nuestra [página de documentación](https://azure.microsoft.com/documentation/services/search/) para encontrar más recursos. También puede ver los vínculos en nuestra [lista de vídeos y tutoriales](search-video-demo-tutorial-list.md) para tener acceso a más información.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

<!---HONumber=AcomDC_0309_2016-->