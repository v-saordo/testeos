<properties
	pageTitle="Instalación del Kit de herramientas de Azure para Eclipse"
	description="Averigüe cómo instalar el Kit de herramientas de Azure para Eclipse."
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
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Instalación del Kit de herramientas de Azure para Eclipse #

El kit de herramientas de Azure para Eclipse ofrece plantillas y funciones que permiten crear, desarrollar, probar e implementar aplicaciones de Azure fácilmente con el entorno de desarrollo de Eclipse. Es un proyecto de código abierto, cuyo código fuente está disponible con la licencia de Apache 2.0 del sitio del proyecto en GitHub en la siguiente dirección URL:

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

En los pasos siguientes se muestra cómo instalar el Kit de herramientas de Azure para Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Para instalar el Kit de herramientas de Azure para Eclipse ##

1. Inicie Eclipse.
2. En Eclipse, en el menú, haga clic en <strong>Ayuda</strong> y luego en <strong>Instalar nuevo software</strong>, como se muestra en el siguiente diagrama.
    ![][ic590123]
3. En el cuadro de diálogo <strong>Software disponible</strong>, en el cuadro de texto <strong>Trabajar con</strong>, escriba <strong>http://dl.msopentech.com/eclipse</strong> seguido de la tecla <strong>Entrar</strong>.
4. En el panel <strong>Nombre</strong>, active <strong>Kit de herramientas de Azure para Eclipse</strong>, y desactive <strong>Ponerse en contacto con todos los sitios de actualización durante la instalación para encontrar el software necesario</strong>. La pantalla debe parecerse a la siguiente:
    ![][ic719482]
5. Si expande el <strong>Kit de herramientas de Azure para Eclipse</strong>, verá los siguientes elementos:
    * **Filtro de servicios de control de acceso de Azure**: este componente ofrece compatibilidad para autenticar a los usuarios de aplicación con ACS de Azure.
    * **Complemento común de Azure**: este componente contiene la funcionalidad en la que se basan los demás componentes.
    * **Kit de herramientas de Azure para Eclipse**: este componente contiene la lógica de la configuración del proyecto, el Asistente para la publicación en la nube y la interfaz de usuario.
    * **Microsoft JDBC Driver 4.0 para SQL Server**: este componente simplifica el desarrollo de aplicaciones con la Base de datos SQL.
    * **Paquete para bibliotecas de cliente Apache Qpid de JMS**: este componente ofrece la biblioteca de cliente JMS del proyecto de Apache Qpid para habilitar su aplicación para que use la mensajería basada en el protocolo de colas de mensajería avanzada (AMQP) en Azure.
    * **Paquetes para bibliotecas de Azure para Java**: este componente le permite compilar aplicaciones de Azure en Java que le permitan aprovechar los recursos informáticos de la nube escalable de Azure.
    * **Complemento de Application Insights para Java**: este componente permite usar los servicios de análisis y registro de telemetría de Azure para sus aplicaciones e instancias de servidor.
6. Haga clic en **Siguiente**. (Si se producen retrasos inusuales al instalar el kit de herramientas, asegúrese de que la opción **Ponerse en contacto con todos los sitios de actualización durante la instalación para encontrar el software necesario** está desactivada.)
7. En el cuadro de diálogo **Detalles de instalación**, haga clic en **Siguiente**.
8. En el cuadro de diálogo **Revisar licencias**, revise los términos de los contratos de licencia. Si acepta los términos de los contratos de licencia, haga clic en **Acepto los términos de los contratos de licencia** y luego haga clic en **Finalizar**. (En los pasos restantes se supone que acepta los términos de los contratos de licencia. Si no acepta los términos de los contratos de licencia, salga del proceso de instalación.)
9. Si se le solicita que reinicie Eclipse para completar la instalación, haga clic en **Reiniciar ahora**.

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hola a todos para Azure en Eclipse][]

[Novedades del kit de herramientas de Azure para Eclipse][]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Creación de una aplicación Hola a todos para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Web Platform Installer (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[Instalador de plataforma web (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[Novedades del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_0114_2016-->
