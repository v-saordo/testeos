<properties
    pageTitle="Habilitar la afinidad de la sesión con el kit de herramientas de Azure para Eclipse"
    description="Averigüe cómo habilitar la afinidad de sesión con el kit de herramientas de Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# Habilitar la afinidad de la sesión #

En el Kit de herramientas de Azure para Eclipse, puede habilitar la afinidad de la sesión HTTP o "sesiones permanentes", para sus roles. En la siguiente imagen se muestra el cuadro de diálogo de propiedades **Equilibrio de carga** que se us para habilitar la característica de afinidad de sesión:

![][ic719492]

## Para habilitar la afinidad de sesión para su rol ##

1. Haga clic con el botón secundario en el rol en el Explorador de proyectos de Eclipse, haga clic en **Azure** y, luego, en **Equilibrio de carga**.
1. En el cuadro de diálogo **Propiedades del equilibrio de carga de WorkerRole1**:
    1. Active **Habilitar afinidad de sesión HTTP (sesiones permanentes) para este rol**.
    1. Para **Punto de conexión de entrada que se va a usar**, seleccione un punto de conexión de entrada que se usará, por ejemplo, **http (public:80, private:8080)**. Su aplicación debe usar este punto de conexión como su punto de conexión HTTP. Puede habilitar varios puntos de conexión para su rol, pero solo puede seleccionar uno de ellos para admitir sesiones permanentes.
    1. Vuelva a compilar la aplicación.

Una vez habilitada, si tiene más de una instancia de rol, las solicitudes HTTP procedentes de un cliente concreto seguirán controlándose por la misma instancia de rol.

El Kit de herramientas de Eclipse permite esto al instalar un módulo IIS especial llamado enrutamiento de solicitudes de aplicaciones (ARR) en cada una de sus instancias de rol. ARR vuelve a enrutar las solicitudes HTTP para la instancia de rol adecuada. El kit de herramientas vuelve a configurar automáticamente el punto de conexión seleccionado de manera que el tráfico HTTP entrante se enruta primero al software de ARR. El kit de herramientas también crea un nuevo punto de conexión interno al que está configurado el servidor Java para escuchar. Ese es el punto de conexión usado por ARR para volver a enrutar el tráfico HTTP a la instancia de rol adecuada. De este modo, cada instancia de rol de su implementación de varias instancias actúa como proxy inverso para todas las demás instancias, lo que permite sesiones permanentes.

## Notas sobre la afinidad de sesión ##

* La afinidad de sesión no funciona en el emulador de proceso. La configuración se puede aplicar en el emulador de proceso sin interferir con su proceso de compilación o ejecución del emulador de proceso, pero la propia característica no funciona en el emulador de proceso.
* Al habilitar la afinidad de sesión aumentará la cantidad del espacio de disco usado por la implementación en Azure, puesto que se descargará software adicional y se instalará en sus instancias de rol cuando se inicie su servicio en la nube de Azure.
* El tiempo para inicializar cada rol será superior.
* Se agregará un punto de conexión interno para que funcione como enrutador de tráfico, como se ha mencionado anteriormente.

Para un ejemplo de cómo mantener los datos de sesión cuando la afinidad de sesión está habilitada, vea [Cómo mantener datos de sesión con la afinidad de sesión][].

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hello World para Azure en Eclipse][]

[Instalación del Kit de herramientas de Azure para Eclipse][]

[Cómo mantener los datos de sesión con afinidad de sesión][]

Para más información sobre el uso de Azure con Java, vea el [Centro de desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro de desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creación de una aplicación Hello World para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Cómo mantener datos de sesión con la afinidad de sesión]: http://go.microsoft.com/fwlink/?LinkID=699539
[Cómo mantener los datos de sesión con afinidad de sesión]: http://go.microsoft.com/fwlink/?LinkID=699539
[Instalación del Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!---HONumber=AcomDC_0302_2016-->