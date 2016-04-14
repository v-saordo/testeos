<properties 
   pageTitle="Configuración y uso del emulador de almacenamiento con Visual Studio | Microsoft Azure"
   description="Configuración y uso del emulador de almacenamiento con Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="tarcher" />

# Configuración y uso del emulador de almacenamiento con Visual Studio

El entorno de desarrollo SDK de Azure incluye el emulador de almacenamiento, una utilidad que simula los servicios de almacenamiento de blobs, colas y tablas disponibles en Azure, en el equipo de desarrollo local. Si está creando un servicio en la nube que emplea los servicios de almacenamiento de Azure o escribiendo una aplicación externa que llama a los servicios de almacenamiento, puede probar el código localmente con el emulador de almacenamiento. Azure Tools para Microsoft Visual Studio integra la administración del emulador de almacenamiento en Visual Studio. Azure Tools inicializa la base de datos del emulador de almacenamiento cuando se usa por primera vez, inicia el servicio del emulador de almacenamiento cuando ejecuta o depura el código desde Visual Studio y proporciona acceso de solo lectura a los datos del emulador de almacenamiento mediante el Explorador de almacenamiento de Azure.

Para obtener información detallada sobre el emulador de almacenamiento, incluidos los requisitos del sistema e instrucciones de configuración personalizada, consulte [Uso del emulador de almacenamiento de Azure para desarrollo y pruebas](./storage/storage-use-emulator/).

>[AZURE.NOTE]Existen algunas diferencias de funcionalidad entre el emulador de almacenamiento y los servicios de almacenamiento de Azure. Consulte [Diferencias entre el emulador de almacenamiento y los servicios de almacenamiento de Azure](./storage/storage-use-emulator) en la documentación del SDK de Azure para obtener información sobre las diferencias concretas.

## Configuración de una cadena de conexión para el emulador de almacenamiento

Para acceder al emulador de almacenamiento desde el código dentro de un rol, deberá configurar una cadena de conexión que señale al emulador de almacenamiento y que se pueda cambiar después para que señale a una cuenta de almacenamiento de Azure. Una cadena de conexión es una opción de configuración que el rol puede leer en tiempo de ejecución para conectarse a una cuenta de almacenamiento. Para obtener más información sobre cómo crear cadenas de conexión, consulte el artículo sobre cómo [configurar la aplicación de Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE]Puede devolver una referencia a la cuenta del emulador de almacenamiento desde el código mediante la propiedad **DevelopmentStorageAccount**. Este enfoque funciona correctamente si desea acceder al emulador de almacenamiento desde el código, pero, si piensa publicar su aplicación en Azure, necesitará crear una cadena de conexión para acceder a su cuenta de almacenamiento de Azure y modificar el código para que use esa cadena de conexión antes de publicarla. Si cambia entre la cuenta del emulador de almacenamiento y una cuenta de almacenamiento de Azure con frecuencia, una cadena de conexión simplificará el proceso.

## Inicialización y ejecución del emulador de almacenamiento

Puede especificar que, cuando ejecute o depure el servicio en Visual Studio, Visual Studio inicie automáticamente el emulador de almacenamiento. En el Explorador de soluciones, abra el menú contextual de su proyecto de **Azure** y elija **Propiedades**. En la pestaña **Desarrollo**, en la lista **Iniciar emulador de almacenamiento de Microsoft Azure**, elija **Verdadero** (si no está ya establecido en ese valor).

La primera vez que ejecute o depure el servicio desde Visual Studio, el emulador de almacenamiento inicia un proceso de inicialización. Este proceso reserva los puertos locales para el emulador de almacenamiento y crea la base de datos del emulador de almacenamiento. Una vez finalizado, no es necesario volver a ejecutar este proceso a menos que se elimine la base de datos del emulador de almacenamiento.

>[AZURE.NOTE]A partir de la versión de junio de 2012 de Azure Tools, el emulador de almacenamiento se ejecuta, de forma predeterminada, en SQL Express LocalDB. En versiones anteriores de Azure Tools, el emulador de almacenamiento se ejecuta en una instancia predeterminada de SQL Express 2005 o 2008, que debe instalar para poder instalar el SDK de Azure. También puede ejecutar el emulador de almacenamiento en una instancia con nombre de SQL Express o en una instancia con nombre o predeterminada de Microsoft SQL Server. Si necesita configurar el emulador de almacenamiento para que se ejecute en una instancia distinta de la predeterminada, consulte [Uso del emulador de almacenamiento de Azure para desarrollo y pruebas](./storage/storage-use-emulator/).

El emulador de almacenamiento proporciona una interfaz de usuario para ver el estado de los servicios de almacenamiento local y para iniciarlos, detenerlos y restablecerlos. Una vez que se ha iniciado el servicio del emulador de almacenamiento, puede mostrar la interfaz de usuario o iniciar o detener el servicio haciendo clic con el botón derecho en el icono del área de notificación para el Emulador de Microsoft Azure en la barra de tareas de Windows.

## Visualización de los datos del emulador de almacenamiento en el Explorador de servidores

El nodo Almacenamiento de Azure en el Explorador de servidores permite ver los datos y cambiar la configuración para los datos de blob y tabla en sus cuentas de almacenamiento, incluido el emulador de almacenamiento. Consulte [Exploración y administración de recursos de almacenamiento con el Explorador de servidores](https://msdn.microsoft.com/library/azure/ff683677.aspx) para obtener más información.

<!---HONumber=AcomDC_1223_2015-->