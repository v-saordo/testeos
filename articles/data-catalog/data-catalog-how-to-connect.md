<properties
   pageTitle="Conexión a orígenes de datos"
   description="Artículo de procedimientos que indica cómo conectarse a orígenes de datos que se detectan con el Catálogo de datos de Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/10/2015"
   ms.author="maroche"/>


# Conexión a orígenes de datos

## Introducción
El **Catálogo de datos de Microsoft Azure** es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección de orígenes de datos empresariales. En otras palabras, el **Catálogo de datos de Azure** consiste en ayudar a las personas a detectar, comprender y usar orígenes de datos, y en ayudar a las organizaciones a obtener más valor de sus datos. Un aspecto clave de este escenario es el uso de los datos: cuando un usuario detecta un origen de datos y comprende su propósito, el paso siguiente es la conexión al origen de datos para utilizar sus datos.

##Ubicaciones de orígenes de datos
Durante el registro del origen de datos, el **Catálogo de datos de Azure** recibe los metadatos sobre el origen de datos. Estos metadatos incluyen los detalles de la ubicación del origen de datos. Los detalles de la ubicación varían según el origen de datos, pero siempre contendrán la información necesaria para conectarse. Por ejemplo, la ubicación de una tabla de SQL Server incluye el nombre del servidor, el nombre de la base de datos, el nombre del esquema y el nombre de la tabla; mientras que la ubicación de un informe de SQL Server Reporting Services incluye el nombre del servidor y la ruta de acceso al informe. Otros tipos de orígenes de datos tendrán ubicaciones que reflejan la estructura y las capacidades del sistema de origen.

##Herramientas de cliente integradas
La manera más sencilla de conectarse a un origen de datos es usar el menú "Abrir en…" del portal del **Catálogo de datos de Azure**. En este menú se muestra una lista de opciones para conectarse al recurso de datos seleccionado. Cuando se usa la vista de iconos predeterminada, este menú está disponible en cada icono.

 ![Apertura de una tabla de SQL Server en Excel desde el icono de recurso de datos](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Al utilizar la vista de lista, el menú está disponible en la barra de búsqueda de la parte superior de la ventana del portal.

 ![Apertura de un informe de SQL Server Reporting Services en el Administrador de informes desde la barra de búsqueda](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

##Sus propios datos y herramientas
Las opciones disponibles en el menú dependerán del tipo de recurso de datos actualmente seleccionado. Por supuesto, no se incluyen todas las herramientas posibles en el menú "Abrir en…", pero aun así es fácil conectarse al origen de datos con cualquier herramienta de cliente. Cuando se selecciona un recurso de datos en el portal del **Catálogo de datos de Azure**, se muestra la ubicación completa en el panel de propiedades.

 ![Información de conexión de una tabla de SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Los detalles de la información de conexión variarán según el tipo de origen de datos, pero la información incluida en el portal le proporcionará todo lo que necesita para conectarse al origen de datos en cualquier herramienta de cliente. Los usuarios pueden copiar los detalles de conexión de los orígenes de datos que hayan detectado con el **Catálogo de datos de Azure**, lo que les permite trabajar con los datos en su herramienta preferida.

##Permisos de origen de datos y conexión
Aunque el **Catálogo de datos de Azure** permite que los orígenes de datos sean detectables, el control del acceso a los propios datos sigue siendo responsabilidad del administrador o propietario del origen de datos. La detección de un origen de datos en el **Catálogo de datos de Azure** no otorga al usuario ningún permiso para tener acceso al propio origen de datos.

Con el objetivo de simplificar las cosas para los usuarios que detecten un origen de datos, pero no tengan permiso para obtener acceso a sus datos, dichos usuarios pueden proporcionar información en la propiedad de solicitud de acceso al anotar un origen de datos. La información que se incluye aquí (incluidos los vínculos al proceso o el punto de contacto para obtener acceso a los datos de origen) se presenta junto con la información de ubicación del origen de datos en el portal.

 ![Información de conexión con las instrucciones de solicitud de acceso proporcionadas](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##Resumen
Al registrar un origen de datos con el **Catálogo de datos de Azure**, se consigue que esos datos sean detectables mediante la copia de los metadatos descriptivos y estructurales del origen de datos en el servicio Catálogo. Cuando se registra y detecta un origen de datos, los usuarios pueden conectarse al origen de datos desde el menú "Abrir en…" del portal del **Catálogo de datos de Azure** o mediante sus herramientas de datos favoritas.

<!---HONumber=Nov15_HO3-->