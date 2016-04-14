<properties
	pageTitle="Escalado automático de conjuntos de escalado de máquinas virtuales | Microsoft Azure"
	description="Empezar a crear y administrar los primeros conjuntos de escala de máquinas virtuales de Azure con Azure PowerShell"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# Escalado automático de máquinas en un conjunto de escalado de máquinas virtuales

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-vmss-walkthrough-cli.md)
- [Azure PowerShell](virtual-machines-vmss-walkthrough.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Los conjuntos de escala de máquinas virtuales facilitan la implementación y administración de máquinas virtuales idénticas como un conjunto. Los conjuntos de escala proporcionan una capa de proceso altamente escalable y personalizable para aplicaciones de gran escala y admiten imágenes de la plataforma Windows, imágenes de la plataforma Linux, imágenes personalizadas y extensiones. Para más información acerca de los conjuntos de escala, consulte [Conjuntos de escala de máquinas virtuales](virtual-machines-vmss-overview.md).

Este tutorial muestra cómo crear un conjunto de escala de máquinas virtuales de Windows y cómo escalar automáticamente los equipos en el conjunto. Esto se hace creando una plantilla del Administrador de recursos de Azure e implementándola con Azure PowerShell. Para más información sobre las plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

La plantilla que cree en este tutorial será similar a cualquier plantilla que pueda encontrarse en la galería de plantillas. Para más información, consulte [Implementación de un conjunto simple de escala de máquinas virtuales con máquinas virtuales de Windows y un Jumpbox](https://azure.microsoft.com/documentation/templates/201-vmss-windows-jumpbox/).

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [virtual-machines-vmss-preview-ps](../../includes/virtual-machines-vmss-preview-ps-include.md)]

## Paso 1: Creación de un grupo de recursos y una cuenta de almacenamiento

1. **Inicie sesión en Microsoft Azure**. Abra la ventana de Microsoft Azure PowerShell y ejecute **Login-AzureRmAccount**.

2. **Cree un grupo de recursos**: todos los recursos deben implementarse en un grupo de recursos. Para este tutorial, asigne el nombre **vmss-vmsstestrg1** al grupo de recursos. Consulte [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx).

3. **Implemente una cuenta de almacenamiento en el nuevo grupo de recursos**: este tutorial usa varias cuentas de almacenamiento para facilitar el conjunto de escala de máquinas virtuales. Use [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) para crear una cuenta de almacenamiento denominada **vmsstestsa**. Mantenga la ventana de Azure PowerShell abierta para llevar a cabo pasos explicados más adelante en este tutorial.

## Paso 2: Creación de la plantilla
Las plantillas del Administrador de recursos de Azure le permiten implementar y administrar los distintos recursos de Azure en conjunto mediante una descripción de JSON de los recursos y los parámetros de implementación asociados.

1. En el editor de texto que prefiera, cree el archivo C:\\VMSSTemplate.json y agregue la estructura inicial de JSON para admitir la plantilla.

	```
	{
		"$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
		}
		"variables": {
		}
		"resources": [
		]
	}
	```

2. Los parámetros no siempre son necesarios, pero facilitan la administración de la plantilla. Proporcionan una manera de especificar valores para la plantilla, describen el tipo del valor, el valor predeterminado, en caso de ser necesario, y, posiblemente, los valores permitidos del parámetro.

	Agregue estos parámetros en el elemento primario de los parámetros que agregó a la plantilla:

	- Un nombre para la máquina virtual de jumpbox que se utiliza para tener acceso a las máquinas del conjunto de escala.
	- Un nombre para la cuenta de almacenamiento donde se almacena la plantilla.
	- El número de instancias de máquinas virtuales para crear inicialmente en el conjunto de escala.
	- El nombre y la contraseña de la cuenta de administrador en las máquinas virtuales.
	- Un prefijo para los recursos que se crean en el grupo de recursos.


	```
	"vmName": {
		"type": "string"
	},
	"vmSSName": {
		"type": "string"
	},
	"instanceCount": {
		"type": "string"
	},
	"adminUsername": {
		"type": "string"
	},
	"adminPassword": {
		"type": "securestring"
	},
	"resourcePrefix": {
		"type": "string"
	}
	```

3. Pueden usarse variables en una plantilla para especificar los valores que pueden cambiar con frecuencia o los valores que se deben crear a partir de una combinación de valores de parámetro.

	Agregue estas variables en el elemento primario de las variables que agregó a la plantilla:

	- Nombres DNS que usan las interfaces de red.
	- El tamaño de las máquinas virtuales que se usan en el conjunto de escala. Para más información sobre los tamaños de máquina virtual, consulte [Tamaños de máquina virtual](virtual-machines-size-specs.md).
	- La información de la imagen de plataforma para definir el sistema operativo que se ejecutará en las máquinas virtuales en el conjunto de escala. Para más información acerca de la selección de imágenes, consulte [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](resource-groups-vm-searching.md).
	- Los nombres de direcciones IP y los prefijos para la red virtual y las subredes.
	- Los nombres y los identificadores de la red virtual, el equilibrador de carga y las interfaces de red.
	- Los nombres de cuentas de almacenamiento para las cuentas asociadas a las máquinas en el conjunto de escala.
	- Configuración de la extensión de diagnósticos que se instala en las máquinas virtuales. Para más información sobre la extensión de diagnósticos, consulte [Crear una máquina virtual de Windows con supervisión y diagnóstico mediante la plantilla del Administrador de recursos de Azure](virtual-machines-extensions-diagnostics-windows-template.md)

	```
	"apiVersion": "2015-06-15"
	"dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
	"dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
	"vmSize": "Standard_A0",
	"imagePublisher": "MicrosoftWindowsServer",
	"imageOffer": "WindowsServer",
	"imageVersion": "2012-R2-Datacenter",
	"addressPrefix": "10.0.0.0/16",
	"subnetName": "Subnet",
	"subnetPrefix": "10.0.0.0/24",
	"publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
	"publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
	"loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
	"virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
	"nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
	"nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	"publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
	"publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
	"lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	"nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
	"frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	"storageAccountType": "Standard_LRS",
	"storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="es-ES"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
	```

4. En este tutorial, implemente los recursos y las extensiones siguientes:

 - Microsoft.Storage/storageAccounts
 - Microsoft.Network/virtualNetworks
 - Microsoft.Network/publicIPAddresses
 - Microsoft.Network/loadBalancers
 - Microsoft.Network/networkInterfaces
 - Microsoft.Compute/virtualMachines
 - Microsoft.Compute/virtualMachineScaleSets
 - Microsoft.Insights.VMDiagnosticsSettings
 - Microsoft.Insights/autoscaleSettings

	Para más información sobre los recursos del Administrador de recursos, consulte [Proveedores de procesos, redes y almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md).

	Agregue el recurso de la cuenta de almacenamiento en el elemento primario de recursos que agregó a la plantilla. Esta plantilla utiliza un bucle para crear las 5 cuentas de almacenamiento recomendadas donde se almacenan los discos del sistema operativo y los datos de diagnóstico. Este conjunto de cuentas puede admitir hasta 100 máquinas virtuales en un conjunto de escala, lo cual es el máximo actual. Cada cuenta de almacenamiento se denomina con un indicador de letra, que se definió en las variables, y se combina con el sufijo que proporcionó en los parámetros de la plantilla.

	```
	{
		"type": "Microsoft.Storage/storageAccounts",
		"name": "[concat(variables('resourcePrefix'), parameters('storageAccountSuffix')[copyIndex()])]",
		"apiVersion": "2015-05-01-preview",
		"copy": {
			"name": "storageLoop",
			"count": 5
		},
		"location": "[resourceGroup().location]",
		"properties": {
			"accountType": "[variables('storageAccountType')]"
		}
	},
	```

5. Agregue el recurso de red virtual. Consulte [Proveedor de recursos de red](../virtual-network/resource-groups-networking.md) para más información.

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/virtualNetworks",
		"name": "[variables('virtualNetworkName')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"addressSpace": {
				"addressPrefixes": [
					"[variables('addressPrefix')]"
				]
			},
			"subnets": [
				{
					"name": "[variables('subnetName')]",
					"properties": {
						"addressPrefix": "[variables('subnetPrefix')]"
					}
				}
			]
		}
	},
	```

6. Agregue los recursos de dirección IP públicos que usan la interfaz de red y el equilibrador de carga.

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP1')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName1')]"
			}
		}
	},
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP2')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName2')]"
			}
		}
	},
	```

7. Agregue el recurso de equilibrador de carga que usa el conjunto de escala. Para más información, consulte [Compatibilidad del Administrador de recursos de Azure con el equilibrador de carga](../load-balancer/load-balancer-arm.md)

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"name": "[variables('loadBalancerName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "loadBalancerFrontEnd",
					"properties": {
						"publicIPAddress": {
							"id": "[variables('publicIPAddressID1')]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "bepool1"
				}
			],
			"inboundNatPools": [
				{
					"name": "natpool1",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('frontEndIPConfigID')]"
						},
						"protocol": "tcp",
						"frontendPortRangeStart": 50000,
						"frontendPortRangeEnd": 50500,
						"backendPort": 3389
					}
				}
			]
		}
	},
	```

8. Agregue el recurso de interfaz de red que utiliza la máquina virtual de jumpbox. Dado que las máquinas en un conjunto de escala de máquinas virtuales no son accesibles directamente mediante una dirección IP pública, se crea una máquina virtual de jumpbox en la misma red virtual como conjunto de escala y se usa para tener acceso remoto a las máquinas en el conjunto.


	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/networkInterfaces",
		"name": "[variables('nicName1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Dynamic",
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
						},
						"subnet": {
							"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
						}
					}
				}
			]
		}
	},
	```


9. Agregue la máquina virtual en la misma red que el conjunto de escala.

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[parameters('vmName')]",
		"location": "[resourceGroup().location]",
      "dependsOn": [
			"[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
		],
		"properties": {
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[parameters('vmName')]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]"
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[variables('imagePublisher')]",
					"offer": "[variables('imageOffer')]",
					"sku": "[variables('imageVersion')]",
					"version": "latest"
				},
				"osDisk": {
					"name": "osdisk1",
					"vhd": {
						"uri":  "[concat('https://',parameters('resourcePrefix'),'saa.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"        
				}
			},
			"networkProfile": {
				"networkInterfaces": [
					{
						"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
					}
				]
			}
		}
	},
	```

10.	Agregue el conjunto de escala de máquinas virtuales y especifique la extensión de diagnósticos que está instalada en todas las máquinas virtuales del conjunto de escala. Muchos de los valores para este recurso son similares al recurso de máquina virtual. Estas son la diferencias principales:

	- **capacity**: especifica cuántas máquinas virtuales se deben inicializar en el conjunto de escala. Puede establecer este valor si especifica un valor para el parámetro instanceCount.

	- **upgradePolicy**: especifica cómo se realizan las actualizaciones a las máquinas virtuales en el conjunto de escala. Manual especifica que solo las máquinas virtuales nuevas se actualicen con los cambios de una plantilla cuando ésta se vuelve a implementar. Automático especifica que todas las máquinas en el conjunto de escala se actualizan y se reinician.

	El conjunto de escala de máquinas virtuales no se crea hasta que todas las cuentas de almacenamiento se crean como se especifica en el elemento dependsOn.

	```
	{
		"type": "Microsoft.Compute/virtualMachineScaleSets",
		"apiVersion": "[variables('apiVersion')]",
		"name": "[parameters('vmSSName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
			"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
		],
		"sku": {
			"name": "[variables('vmSize')]",
			"tier": "Standard",
			"capacity": "[parameters('instanceCount')]"
		},
		"properties": {
			"upgradePolicy": {
				"mode": "Manual"
			},
			"virtualMachineProfile": {
				"storageProfile": {
					"osDisk": {
						"vhdContainers": [
							"[concat('https://', parameters('resourcePrefix'), 'saa.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sag.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sam.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sas.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'say.blob.core.windows.net/vmss')]"
						],
						"name": "vmssosdisk",
						"caching": "ReadOnly",
						"createOption": "FromImage"
					},
					"imageReference": {
						"publisher": "[variables('imagePublisher')]",
						"offer": "[variables('imageOffer')]",
						"sku": "[variables('imageVersion')]",
						"version": "latest"
					}
				},
				"osProfile": {
					"computerNamePrefix": "[parameters('vmSSName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"networkProfile": {
					"networkInterfaceConfigurations": [
						{
							"name": "[variables('nicName2')]",
							"properties": {
								"primary": "true",
								"ipConfigurations": [
									{
										"name": "ip1",
										"properties": {
											"subnet": {
												"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
											},
											"loadBalancerBackendAddressPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
												}
											],
											"loadBalancerInboundNatPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
												}
											]
										}
									}
								]
							}
						}
					]
				},
				"extensionProfile": {
					"extensions": [
						{
							"name": "Microsoft.Insights.VMDiagnosticsSettings",
							"properties": {
								"publisher": "Microsoft.Azure.Diagnostics",
								"type": "IaaSDiagnostics",
								"typeHandlerVersion": "1.5",
								"autoUpgradeMinorVersion": true,
								"settings": {
									"xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
									"storageAccount": "[variables('diagnosticsStorageAccountName')]"
								},
								"protectedSettings": {
									"storageAccountName": "[variables('diagnosticsStorageAccountName')]",
									"storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
									"storageAccountEndPoint": "https://core.windows.net"
								}
							}
						}
					]
				}
			}
		}
	},
	```

11.	Agregue el recurso autoscaleSettings que define cómo el conjunto de escala se ajusta según el uso del procesador en las máquinas del conjunto. Para este tutorial, éstos son los valores importantes:

 - **metricName**: este es el mismo que el contador de rendimiento que definimos en la variable wadperfcounter. Con esta variable, la extensión de diagnósticos recopila los datos del contador **Processor(\_Total)\\% Processor Time**.
 - **metricResourceUri**: este es el identificador de recursos del conjunto de escala de máquinas virtuales.
 - **timeGrain**: ésta es la granularidad de las métricas que se recopilan. En esta plantilla, se establece en 1 minuto.
 - **statistic**: determina cómo se combinan las métricas para dar cabida a la acción de escalado automático. Los valores posibles son: Average, Min y Max. En esta plantilla estamos buscando el uso total de CPU promedio entre las máquinas virtuales del conjunto de escala.
 - **timeWindow**: este es el intervalo de tiempo en que se recopilan los datos de instancia. Debe estar comprendido entre 5 minutos y 12 horas.
 - **timeAggregation**: este determina la manera en que se deberían combinar los datos recopilados en el tiempo. El valor predeterminado es Average. Los valores posibles son: Average, Minimum, Maximum, Last, Total, Count.
 - **operator**: este es el operador que se utiliza para comparar los datos de métrica y el umbral. Los valores posibles son: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
 - **threshold**: este es el valor que desencadena la acción de escalado. En esta plantilla, los equipos se agregan al conjunto de escala que se establece cuando el uso promedio de CPU entre máquinas del conjunto es más de un 50%.
 - **direction**: este determina la acción que se realiza cuando se alcanza el valor de umbral. Los valores posibles son Increase o Decrease. En esta plantilla, se aumenta el número de máquinas virtuales en el conjunto de escala si el umbral es más de un 50% en la ventana de tiempo definido.
 - **type**: este es el tipo de acción que debe realizarse y que se debe establecer en ChangeCount.
 - **value**: este es el número de máquinas virtuales que se agregan o se quitan del conjunto de escala. Este valor debe ser 1 o un valor superior. El valor predeterminado es 1. En esta plantilla, el número de máquinas en el conjunto de escala aumenta en 1 cuando se alcanza el umbral.
 - **cooldown**: es la cantidad de tiempo de espera desde la última acción de escalada hasta antes de que se produzca la siguiente acción. Este valor debe estar comprendido entre 1 minuto y 1 semana.

	```
	{
		"type": "Microsoft.Insights/autoscaleSettings",
		"apiVersion": "2015-04-01",
		"name": "[concat(parameters('resourcePrefix'),'as1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		],
		"properties": {
			"enabled": true,
			"name": "[concat(parameters('resourcePrefix'),'as1')]",
			"profiles": [
				{
					"name": "Profile1",
					"capacity": {
						"minimum": "1",
						"maximum": "10",
						"default": "1"
					},
					"rules": [
						{
							"metricTrigger": {
								"metricName": "\\Processor(_Total)\\% Processor Time",
								"metricNamespace": "",
								"metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
								"timeGrain": "PT1M",
								"statistic": "Average",
								"timeWindow": "PT5M",
								"timeAggregation": "Average",
								"operator": "GreaterThan",
								"threshold": 50.0
							},
							"scaleAction": {
								"direction": "Increase",
								"type": "ChangeCount",
								"value": "1",
								"cooldown": "PT5M"
							}
						}
					]
				}
			],
			"targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		}
	}
	```

12.	Guarde el archivo de plantilla.    

## Paso 3: Carga de la plantilla en el almacenamiento

La plantilla se puede cargar desde la ventana de Microsoft Azure PowerShell siempre que sepa el nombre de cuenta y la clave principal de la cuenta de almacenamiento que creó en el paso 1.

1.	En la ventana de Microsoft Azure PowerShell, puede establecer una variable que especifique el nombre de la cuenta de almacenamiento que implementó en el paso 1.

		$StorageAccountName = "vmstestsa"

2.	Establezca una variable que especifique la clave principal de la cuenta de almacenamiento.

		$StorageAccountKey = "<primary-account-key>"

	Puede obtener esta clave haciendo clic en el icono de llave al ver el recurso de la cuenta de almacenamiento en el portal de Azure.

3.	Cree el objeto de contexto de la cuenta de almacenamiento que se usa para validar las operaciones con la cuenta de almacenamiento.

		$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

4.	Cree un nuevo contenedor de plantillas donde se pueda almacenar la plantilla que creó.

		$ContainerName = "templates"
		New-AzureStorageContainer -Name $ContainerName -Context $ctx  -Permission Blob

5.	Cargue el archivo de plantillas en el nuevo contenedor.

		$BlobName = "VMSSTemplate.json"
		$fileName = "C:" + $BlobName
		Set-AzureStorageBlobContent -File $fileName -Container $ContainerName -Blob  $BlobName -Context $ctx

## Paso 4: Implementación de la plantilla

Ahora que ha creado la plantilla, puede empezar a implementar los recursos. Use este comando para iniciar el proceso:

		New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Cuando presione Entrar, se le pedirá que proporcione valores para las variables que asignó. Proporcione estos valores:

	vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

Para que todos los recursos se implementen correctamente se tardará unos 15 minutos.

>[AZURE.NOTE]También puede hacer uso de la capacidad del portal para implementar los recursos. Para ello, use este vínculo: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## Paso 5: Supervisión de recursos

Puede obtener información acerca de los conjuntos de escala de máquinas virtuales mediante estos métodos:

 - El portal de Azure: actualmente puede obtener una cantidad limitada de información mediante el portal.
 - El [explorador de recursos de Azure](https://resources.azure.com/): esta es la mejor herramienta para explorar el estado actual del conjunto de escala. Siga esta ruta de acceso y debería ver la vista de instancia del conjunto de escala que creó:

		subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell: use este comando para más información:

		Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

 - Conéctese a la máquina virtual de jumpbox igual que lo haría con cualquier otra máquina y, a continuación, podrá obtener acceso remoto a las máquinas virtuales del conjunto de escala para supervisar los procesos individuales.

>[AZURE.NOTE]Una API de REST completa para más información acerca de los conjuntos de escala se puede encontrar en [Conjuntos de escala de máquinas virtuales](https://msdn.microsoft.com/library/mt589023.aspx)

## Paso 6: Eliminación de recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. No es necesario eliminar por separado cada recurso de un grupo de recursos. Puede eliminar el grupo de recursos y se eliminarán automáticamente todos sus recursos.

	Remove-AzureRmResourceGroup -Name vmsstestrg1

Si desea mantener el grupo de recursos, puede eliminar solo el conjunto de escala.

	Remove-AzureRmResource -Name vmsstest1 -ResourceGroupName vmsstestrg1 -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

<!---HONumber=AcomDC_0128_2016-->