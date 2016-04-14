## Perfil del Administrador de tráfico

El Administrador de tráfico y su recurso de punto de conexión secundario permiten el enrutamiento DNS a los puntos de conexión dentro y fuera de Azure. Esta distribución de tráfico se rige por los métodos de directivas de enrutamiento. El Administrador de tráfico también permite supervisar el estado del extremo y desviar el tráfico adecuadamente según el estado de un extremo.

| Propiedad | Descripción |
|---|---|
|**trafficRoutingMethod**| Los valores posibles son *Performance*, *Weighted* y *Priority*. | 
| **dnsConfig** | FQDN para el perfil | 
| **Protocolo** | protocolo de supervisión, los valores posibles son *HTTP* y *HTTPS*|
| **Puerto** | puerto de supervisión |  
| **Ruta de acceso** | ruta de acceso de supervisión |
| **Extremos** | contenedor de recursos de punto de conexión | 

### Extremo 

Un extremo es un recurso secundario de un perfil de Administrador de tráfico. Representa un servicio o extremo web al que se distribuye el tráfico del usuario según la directiva configurada en el recurso de perfil del Administrador de tráfico.

| Propiedad | Descripción | 
|---|---| 
| **Tipo** | el tipo de punto de conexión, los posibles valores son *punto de conexión de Azure*, *punto de conexión externo* y *punto de conexión anidado* | 
| **targetResourceId** | dirección IP pública de servicio o punto de conexión web. Puede tratarse de un extremo de Azure o uno externo. | 
| **Peso** | ponderación del punto de conexión usada en la administración del tráfico. | 
| **Prioridad** | prioridad del punto de conexión, usado para definir una acción de conmutación por error |

Ejemplo de Administrador de tráfico en formato Json:


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## Recursos adicionales

Para más información, lea la [documentación de API de REST para el Administrador de tráfico](https://msdn.microsoft.com/library/azure/mt163664.aspx).

<!---HONumber=AcomDC_1223_2015-->