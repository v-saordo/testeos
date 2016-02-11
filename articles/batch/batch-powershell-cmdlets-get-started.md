<properties
   pageTitle="Introducción a PowerShell para Lote de Azure | Microsoft Azure"
   description="Obtenga una rápida introducción a los cmdlets de Azure PowerShell que puede usar para administrar el servicio Lote de Azure"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="01/21/2016"
   ms.author="danlep"/>

# Introducción a los cmdlets de lotes PowerShell de Azure
Esta es una breve introducción a los cmdlets de Azure PowerShell que puede usar para administrar sus cuentas de Lote y trabajar con sus recursos de Lote tales como grupos, trabajos y tareas. Con los cmdlets de Lote puede realizar muchas de las mismas tareas que puede realizar con las API de Lote y el Portal de Azure. Este artículo se basa en los cmdlets de Azure PowerShell versión 1.0 o posteriores.

Para obtener una lista completa de los cmdlets de Lote y la sintaxis detallada de los cmdlets, consulte la [referencia de los cmdlets de Lote de Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).


## Requisitos previos

* **Azure PowerShell**: consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones para descargar e instalar Azure PowerShell. Como los cmdlets de Lote de Azure se incluyen en el módulo Administrador de recursos de Azure, deberá ejecutar el cmdlet **Login-AzureRmAccount** para conectarse a su suscripción. Encontrará más detalles en [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).



* **Registro en el espacio de nombres del proveedor de Lote (operación única)**: antes de trabajar con las cuentas de Lote, debe registrarse en el espacio de nombres del proveedor de Lote. Esta operación solo debe realizarse una vez por cada suscripción.

    ```
    Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch
    ```

## Administrar claves y cuentas por lotes

### Crear una cuenta de lote

**New-AzureRmBatchAccount** crea una nueva cuenta de Lote en un grupo de recursos especificado. Si no dispone de un grupo de recursos, cree uno mediante el cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx), especificando una de las regiones de Azure en el parámetro **Location**, como, por ejemplo, "Centro de EE. UU.". Por ejemplo:

```
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

A continuación, cree una nueva cuenta de Lote en el grupo de recursos, especificando también un nombre de cuenta para <*account_name*> y la ubicación en la que está disponible el servicio de Lote. La creación de la cuenta puede tardar varios minutos en completarse. Por ejemplo:

```
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE] El nombre de la cuenta de proceso debe ser único para Azure, contener entre 3 y 24 caracteres y usar solo letras minúsculas y números.

### Obtener claves de acceso de cuenta
**Get-AzureBatchAccountKeys** muestra las claves de acceso asociadas a una cuenta de Lote de Azure. Por ejemplo, ejecute lo siguiente para obtener las claves principales y secundarias de la cuenta que creó.

```
$Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### Generar una nueva clave de acceso
**New-AzureBatchAccountKey** genera una nueva clave de cuenta principal o secundaria para una cuenta de Lote de Azure. Por ejemplo, para generar una nueva clave principal para la cuenta de proceso por lotes, escriba:

```
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE] Para generar una nueva clave secundaria, especifique "Secondary" para el parámetro **KeyType**. Deberá volver a generar las claves principales y secundarias por separado.

### Eliminar una cuenta de lote
**Remove-AzureBatchAccount** elimina una cuenta de Lote. Por ejemplo:

```
Remove-AzureRmBatchAccount -AccountName <account_name>
```

Cuando se le pida, confirme que desea quitar la cuenta. La eliminación de cuenta puede tardar unos minutos en completarse.

## Creación de un objeto BatchAccountContext

Para crear y administrar grupos, trabajos, tareas y otros recursos en una cuenta de Lote, primero debe crear un objeto BatchAccountContext para almacenar el nombre de cuenta y las claves:

```
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

Aplique este contexto en los cmdlets que interactúan con el servicio de proceso por lotes mediante el parámetro **BatchContext**.

> [AZURE.NOTE] De forma predeterminada, se utiliza la clave principal de la cuenta para la autenticación, pero se puede seleccionar explícitamente la clave para utilizar cambiando el objeto la propiedad **KeyInUse** del objeto BatchAccountContext: `$context.KeyInUse = "Secondary"`.



## Creación y modificación de recursos de Lote
Use cmdlets como **New-AzureBatchPool**, **New-AzureBatchJob** y **New-AzureBatchTask** para crear recursos en una cuenta de Lote. Hay cmdlets **Get-** y **Set-** correspondientes para actualizar las propiedades de los recursos existentes, y cmdlets **Remove-** para quitar recursos en una cuenta de Lote.

Por ejemplo, el siguiente cmdlet crea un nuevo grupo de Lote configurado para usar máquinas virtuales de tamaño pequeño con la versión del sistema operativo más reciente de la familia 3 (Windows Server 2012), con el número de destino de nodos de proceso determinado por una fórmula de escalado automático. En este caso, la fórmula es simplemente $TargetDedicated=3, que indica que el número de nodos de proceso en el grupo es 3 como máximo. El parámetro **BatchContext** especifica una variable definida anteriormente *$context* como el objeto BatchAccountContext.

```
New-AzureBatchPool -Id "MyAutoScalePool" -VirtualMachineSize "Small" -OSFamily "3" -TargetOSVersion "*" -AutoScaleFormula '$TargetDedicated=3;' -BatchContext $Context
```


## Consulta de grupos, trabajos, tareas y otros detalles

Use cmdlets como **Get-AzureBatchPool**, **Get-AzureBatchJob** y **Get-AzureBatchTask** para consultar las entidades creadas en una cuenta de Lote.


### Consulta de datos

Por ejemplo, use **Get-AzureBatchPools** para encontrar sus grupos. De forma predeterminada, esto consulta todos los grupos de su cuenta, siempre que ya haya almacenado el objeto BatchAccountContext en *$context*:

```
Get-AzureBatchPool -BatchContext $context
```
### Uso de un filtro OData

Puede proporcionar un filtro de OData mediante el parámetro **Filter** para buscar solo los objetos que le interesan. Por ejemplo, puede encontrar todos los grupos cuyos id. empiecen por "myPool":

```
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

Este método no es tan flexible como "Where-Object" en una canalización local. Sin embargo, la consulta se envía al servicio de lote directamente para que todos los filtros se apliquen en el servidor, lo cual ahorra ancho de banda de Internet.

### Uso del parámetro Id

Una alternativa a un filtro OData es el parámetro **Id**. Para consultar un grupo específico con el Id. "myPool":

```
Get-AzureBatchPool -Id "myPool" -BatchContext $context

```
El parámetro **Id** solo admite la búsqueda de id. completo, no con caracteres comodín ni filtros al estilo de OData.



### Uso del parámetro MaxCount

De forma predeterminada, cada cmdlet devuelve un máximo de 1000 objetos. Si se alcanza este límite, puede refinar el filtro para que devuelva menos objetos, o establecer explícitamente un máximo mediante el parámetro **MaxCount**. Por ejemplo:

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

Para quitar el límite superior, establezca **MaxCount** en 0 o menos.

### Usar la canalización

Los cmdlets de lote pueden aprovechar la canalización para enviar datos entre los cmdlets de PowerShell. Esto tiene el mismo efecto que si se especifica un parámetro, pero hace enumerar varias entidades de forma más fácil. Por ejemplo, lo siguiente busca todas las tareas en su cuenta:

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

## Temas relacionados
* [Descarga de Azure PowerShell](http://go.microsoft.com/?linkid=9811175)
* [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)
* [Referencia de cmdlets de Lote de Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Consulta eficaz del servicio Lote](batch-efficient-list-queries.md)

<!---HONumber=AcomDC_0204_2016-->