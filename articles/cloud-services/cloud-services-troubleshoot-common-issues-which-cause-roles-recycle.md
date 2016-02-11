<properties
   pageTitle="Causas comunes del reciclado de roles del servicio en la nube | Microsoft Azure"
   description="Un rol del servicio en la nube que se recicla repentinamente puede causar un considerable tiempo de inactividad. Presentamos algunos de los problemas comunes que pueden provocar el reciclaje de roles, lo que le ayudará a mejorar el tiempo de inactividad."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Problemas comunes que causan el reciclaje de los roles

A continuación se enumeran algunas de las causas comunes de problemas relacionados con la implementación, así como se indican sugerencias para la resolución de estos problemas. Una indicación de que existe un problema con una aplicación es cuando la instancia de rol no se inicia o cuando entra en un bucle de estado **inicializando**, **ocupado** y **deteniendo**.

## Póngase en contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/).

Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).


## Dependencias en tiempo de ejecución que faltan

Si un rol de la aplicación se basa en algún ensamblado que no forma parte de .NET Framework o de la biblioteca administrada de Azure, debe incluir explícitamente ese ensamblado en el paquete de aplicación. Tenga presente que otros marcos de trabajo de Microsoft no están disponibles en Azure de forma predeterminada. Si el rol se basa en uno de esos marcos de trabajo, debe agregar esos ensamblados al paquete de la aplicación.

Antes de compilar y empaquetar la aplicación, compruebe lo siguiente:

- Si usa Visual studio, asegúrese de que la propiedad **Copy Local** se establece en **True** para cada ensamblado del proyecto que no forme parte de Azure SDK o de .NET Framework.

- Asegúrese de que el archivo **web.config** no hace referencia a ningún ensamblado sin usar en el elemento **compilation**.

- El elemento **Build Action** de cada archivo .cshtml se establece en **Content**. Esto garantiza que los archivos aparezcan correctamente en el paquete y permite que otros archivos referenciados aparezcan en el paquete.



## El ensamblado tiene como destino la plataforma equivocada

Azure es un entorno de 64 bits. Por lo tanto, los ensamblados de .NET compilados para un destino de 32 bits no funcionarán en Azure.



## El rol genera excepciones no controladas cuando se inicializa o se detiene

Las excepciones producidas por los métodos de la clase [RoleEntryPoint] que incluya [OnStart], [OnStop] y [Run] son excepciones no controladas. Si se produce una excepción no controlada en uno de estos métodos, el rol se reciclará. Si el rol se recicla de forma repetida, es posible que produzca una excepción no controlada cada vez que intente iniciarse.


## El rol se devuelve del método Run

El método [Run] está pensado para que se ejecute indefinidamente. Si el código invalida el método [Run], podría quedar inactivo indefinidamente. Si se devuelve el método [Run], el rol se recicla.




## Configuración incorrecta de DiagnosticsConnectionString

Si la aplicación usa Diagnósticos de Azure, el archivo de configuración de servicio debe especificar el valor de configuración de `DiagnosticsConnectionString`. Este valor debe especificar una conexión HTTPS a la cuenta de almacenamiento de Azure.

Para asegurarse de que el valor de `DiagnosticsConnectionString` sea correcto antes de implementar el paquete de aplicación en Azure, compruebe lo siguiente:

- El valor de `DiagnosticsConnectionString` apunta a una cuenta de almacenamiento válida de Azure. De forma predeterminada, este valor apunta a la cuenta de almacenamiento emulado, por lo que debe cambiar explícitamente esta configuración antes de implementar el paquete de aplicación. Si no cambia este valor, se produce una excepción cuando la instancia de rol intenta iniciar al monitor de diagnóstico. Esto puede hacer que la instancia de rol se recicle indefinidamente.

- La cadena de conexión se especifica en el [formato](../storage/storage-configure-connection-string.md) siguiente (el protocolo que se especifique debe ser HTTPS). Reemplace *MyAccountName* por el nombre de su cuenta de almacenamiento y *MyAccountKey* por la clave de acceso de su cuenta:

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Si está desarrollando su aplicación con Azure Tools para Microsoft Visual Studio, puede usar las [páginas de propiedades](https://msdn.microsoft.com/library/ee405486) para establecer este valor.



## El certificado exportado no incluye una clave privada

Para ejecutar un rol web con SSL, debe asegurarse de que el certificado de administración exportado incluye la clave privada. Si usa el *administrador de certificados de Windows* para exportar el certificado, asegúrese de seleccionar la opción *Sí, exportar la clave privada*. El certificado debe exportarse al formato PFX, que es el único formato que se admite actualmente.



## Pasos siguientes

Vea más [artículos de solución de problemas](..\?tag=top-support-issue&service=cloud-services) para servicios en la nube.

Consulte más escenarios de reciclaje de roles en la [series de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).




[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx

<!---HONumber=AcomDC_0128_2016-->