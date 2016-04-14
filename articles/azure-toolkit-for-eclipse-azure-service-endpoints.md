<properties
    pageTitle="Puntos de conexión de servicio de Azure"
    description="Describe la configuración del punto de conexión del servicio de Azure en kit de herramientas de Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# Puntos de conexión de servicio de Azure #

Los puntos de conexión de servicio determinan si la aplicación se implementa y se administra en la plataforma global de Azure, es operada en Azure por medio de 21Vianet en China o en una plataforma privada de Azure. El cuadro de diálogo **Extremos de servicio** permite especificar qué puntos de conexión de servicio quiere usar. Para abrir el cuadro de diálogo **Extremos de servicio**, en Eclipse, haga clic en **Ventana**, en **Preferencias**, expanda **Azure** y luego haga clic en **Extremos de servicio**. Al establecer el campo **Conjunto activo** se determinan qué puntos de conexión de servicio de Azure se usarán para los proyectos de Azure en el área de trabajo actual.

Lo siguiente muestra el cuadro de diálogo **Extremos de servicio**.

![][ic719493]

## Para establecer los puntos de conexión del servicio ##

En el cuadro de diálogo **Extremos de servicio**, realice una de las siguientes acciones:

* Si quiere usar la plataforma de Azure global, en la lista desplegable **Conjunto activo**, seleccione **windowsazure.com** y haga clic en **Aceptar**.
* Si quiere usar Azure operado por 21Vianet en China, en la lista desplegable **Conjunto activo**, seleccione **windowsazure.cn (China)** y haga clic en **Aceptar**.
* Si quiere usar una plataforma de Azure privada:
    1. Haga clic en **Editar**.
    2. Se abre un cuadro de diálogo en el que se le informa de que se cerrará el cuadro de diálogo **Extremos de servicio** y de que se abrirá el archivo de conjuntos de preferencias. Haga clic en **Aceptar**.
    3. En el archivo preferencesets.xml, cree un nuevo elemento `preferenceset`. Para este elemento nuevo, cree los atributos `name`, `blob`, `management`, `portalURL` y `publishsettings` y agregue valores para ellos que se correspondan a su plataforma de Azure privada. Puede usar los valores ofrecidos para los elementos `preferenceset` existentes como plantillas. **Nota**: el valor usado para el atributo `blob` debe contener el texto "blob" en la dirección URL.
    4. Guarde y cierre preferencesets.xml.
    5. Vuelva a abrir el cuadro de diálogo **Extremos de servicio**.
    6. En la lista desplegable **Conjunto activo**, seleccione el conjunto activo que ha creado y haga clic en **Aceptar**.
    7. Cuando haya creado su elemento `preferenceset` de la plataforma de Azure privada, para cambiar sus valores asignados, haga clic en el botón **Editar** del cuadro de diálogo **Extremo de servicios**. También puede crear varios elementos `preferenceset` de la plataforma de Azure privada, si así lo desea.

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Instalación del Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hola a todos para Azure en Eclipse][]

Para más información sobre el uso de Azure con Java, vea el [Centro de desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro de desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creación de una aplicación Hola a todos para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalación del Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!---HONumber=AcomDC_0114_2016-->