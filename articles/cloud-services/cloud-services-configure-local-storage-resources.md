<properties
pageTitle="Configuración de recursos de almacenamiento local en Servicios en la nube de Azure"
description=""
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# Configuración de recursos de almacenamiento local

Un recurso de almacenamiento local es un directorio reservado en el sistema de archivos de la máquina virtual en la que se ejecuta una instancia de un rol. Puede almacenar información en la instancia de máquina virtual para que el código que se ejecuta en la instancia pueda acceder al recurso de almacenamiento local cuando necesite escribir en un archivo o leer de este. Por ejemplo, un recurso de almacenamiento local se puede utilizar para almacenar datos en memoria caché por si fuera necesario volver a tener acceso a los mismos mientras el servicio se está ejecutando en Azure. También puede configurar el recursos de almacenamiento local para almacenar archivos durante el inicio. Para obtener más información sobre cómo configurar recursos de almacenamiento local para el inicio, consulte [Uso del almacenamiento local para almacenar archivos durante el inicio](https://msdn.microsoft.com/library/azure/hh974419.aspx).

Los recursos de almacenamiento local se declaran en el archivo de definición de servicio. Puede declarar cualquier número de recursos de almacenamiento local para un rol. Cada recurso de almacenamiento local se reserva para cada instancia de ese rol. La cantidad mínima de espacio en disco que puede asignar a un recurso de almacenamiento local es 1 MB. La cantidad máxima que puede asignar a cualquier recurso determinado local depende del tamaño de la máquina virtual que se especifique para el rol. El tamaño de la máquina virtual dispone de una asignación total para almacenamiento y el espacio total asignado para todos los recursos de almacenamiento local declarados para un rol no puede superar el tamaño máximo asignado a dicho tamaño de la máquina virtual. Para obtener más información sobre la cantidad máxima de espacio en disco local que se asigna a cada tamaño de la máquina virtual, vea [Configuración de los tamaños de los Servicios en la nube](https://msdn.microsoft.com/library/azure/ee814754.aspx).

> [AZURE.NOTE]
>
-   Tenga en cuenta que es responsabilidad del desarrollador asegurarse de que la cantidad de espacio en disco que se solicita para un recurso de almacenamiento local no supera la cantidad máxima asignada a una máquina virtual. Si configura un recurso de almacenamiento local para que sea mayor que el máximo permitido, no se producirá un error hasta que se intente realizar una operación de escritura que supere el máximo permitido. En ese caso, la operación de escritura no se realizará correctamente y aparecerá un mensaje de error sobre espacio insuficiente en disco. El modelo de procesamiento para Azure es de ensayo y error. Si recibe un error de espacio insuficiente en disco puede controlar el error y dejar libre algo de espacio en disco. Después, puede intentar realizar la operación de escritura de nuevo.
-   Puede especificar que se conserve un recurso de almacenamiento local cuando se reciclan instancias. Sin embargo, no se garantiza que duren los datos que se guardan en el sistema de archivos local de la máquina virtual. Si el rol necesita que los datos duren, se recomienda utilizar una unidad de Azure para almacenar datos de archivos. El servicio Blob de Azure realiza copias de seguridad de las unidades de Azure, por lo que su durabilidad queda garantizada.  
>


## Adición de un recurso de almacenamiento local

Para declarar un recurso de almacenamiento local en el archivo de definición de servicio, agregue el elemento **LocalResources** como elemento secundario de un elemento **WebRole** o el elemento **WorkerRole**. Luego, agregue un elemento **LocalStorage** para representar el recurso. El elemento **LocalStorage** tiene tres atributos:

-   *name*
-   *sizeInMB*: especifica el tamaño deseado para este recurso de almacenamiento local
-   *cleanOnRoleRecycle*: especifica si el recurso de almacenamiento local debe limpiarse íntegramente cuando se recicla una instancia de rol o si debe conservarse mientras dure el ciclo de vida del rol. El valor predeterminado es **true**.

El siguiente archivo de definición de servicio muestra dos recursos de almacenamiento local que se declaran para un rol web:

	<?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

Para obtener más información sobre el archivo de definición de servicio, vea [Esquema de definición del servicio de Azure (archivo .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx).

> [AZURE.NOTE]Si utiliza Azure Tools para Microsoft Visual Studio, puede definir un recurso de almacenamiento local en las páginas **Propiedades** del rol. Para obtener más información, consulte [Configuración de la aplicación de Azure con Visual Studio](https://msdn.microsoft.com/library/ee405486.aspx).

## Acceso mediante programación a un recurso de almacenamiento local

Para acceder al recursos de almacenamiento local, la aplicación debe recuperar la ruta de acceso del método [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). A continuación, puede utilizar las operaciones de lectura y escritura de archivos estándar para leer y escribir el contenido del recurso de almacenamiento local. Por ejemplo, el ejemplo siguiente muestra cómo leer el contenido de un archivo llamado **MyTest.txt** desde el recurso de almacenamiento local y mostrarlo en la página principal de una aplicación MVC 3:

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## Acceso a un recurso de almacenamiento local en tiempo de ejecución

La biblioteca administrada de Azure proporciona clases para acceder al recurso de almacenamiento local desde el código que se ejecuta en una instancia de rol. El método [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) devuelve una referencia a un objeto [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx) con nombre.

Dado que el objeto **LocalResource** representa un directorio, este se puede leer y escribir mediante las clases estándar de E/S de archivos .NET. Para determinar la ruta de acceso al directorio del recurso de almacenamiento local, utilice la propiedad [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx). Esta propiedad devuelve la ruta de acceso completa al recurso de almacenamiento local, lo cual incluye el directorio de recursos con nombre. Por ejemplo, si el servicio se está ejecutando en el entorno de desarrollo, el recurso de almacenamiento local se definirá en el sistema de archivos local y la propiedad **RootPath** devolverá un valor similar al siguiente:


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

Cuando el servicio se implementa en Azure, la ruta de acceso al recurso de almacenamiento local incluye el identificador de implementación y la propiedad **RootPath** devuelve un valor similar al siguiente:


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

El código que se ejecuta en una instancia de rol puede acceder a un recurso de almacenamiento local que se define para ese rol desde el momento en que la instancia se pone en línea hasta que se deja sin conexión.

## Pasos siguientes

- [Configurar un servicio en la nube para Azure](https://msdn.microsoft.com/library/azure/hh124108.aspx)

<!---HONumber=Oct15_HO3-->