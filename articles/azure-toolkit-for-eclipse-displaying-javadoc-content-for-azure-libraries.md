<properties
    pageTitle="Visualización del contenido de Javadoc en Eclipse para el paquete de bibliotecas de Azure para Java"
    description="Cómo mostrar el contenido de Javadoc para las bibliotecas de Azure en Eclipse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="02/26/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# Visualización del contenido de Javadoc en Eclipse para el paquete de bibliotecas de Azure para Java #

El contenido de Javadoc de las bibliotecas de Azure para Java se puede ver en el entorno de Eclipse asociando el contenido de Javadoc a las bibliotecas de Azure para Java. En los pasos siguientes se muestra cómo usar esta funcionalidad en Eclipse.

Este procedimiento supone que ya ha agregado la biblioteca de Azure para Java a su ruta de acceso de compilación.

## Para mostrar el contenido de Javadoc en Eclipse para las bibliotecas de Azure para Java ##

* En el Explorador de proyectos de Eclipse, en la sección del proyecto **Bibliotecas de referencia**, abra el menú contextual de la biblioteca de Azure para JAR de Java. Por ejemplo, **microsoft-windowsazure-api-0.1.0.jar** (el número de versión puede ser diferente, en función de la versión que haya instalado).
* Haga clic en **Propiedades**.
* En el cuadro de diálogo **Propiedades**, en el panel izquierdo, haga clic en **Ubicación de Javadoc**. Aparecerá el cuadro de diálogo **Ubicación de Javadoc**.
* Puede especificar una **URL de Javadoc** o un **Javadoc en archivo**.
    * Si elige especificar una **URL de Javadoc**, use las direcciones URL como ****http://dl.windowsazure.com/javadoc** o ****http://dl.windowsazure.com/storage/javadoc**.
* Si decide usar **Javadoc en archivo**, puede especificar un archivo externo o un archivo de área de trabajo. Realice su elección y examine o valide según sea necesario. En el ejemplo siguiente se asocian las bibliotecas de Azure para Java con el JAR de Javadoc correspondiente que se ha descargado localmente en una carpeta denominada **c:\\MyAzureJARs**. ![][ic553487]
* *Opcional*: haga clic en **Validar**. Aquí podrían mostrarse posibles problemas con el JAR de Javadoc.
* Haga clic en **Aceptar**.

Una vez asociado a la biblioteca, el contenido de Javadoc debe mostrarse dentro del IDE de Eclipse. Por ejemplo, si `blob` se define del tipo `CloudBlockBlob` dentro de su código, el siguiente es un ejemplo del contenido de Javadoc que aparece cuando escribe `blob.acquireLease` en el código:

![][ic553488]

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hello World para Azure en Eclipse][]

[Instalación del kit de herramientas de Azure para Eclipse][]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creación de una aplicación Hello World para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalación del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!---HONumber=AcomDC_0302_2016-->