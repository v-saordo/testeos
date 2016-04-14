## Equilibrador de carga
Se utiliza un equilibrador de carga cuando desea escalar sus aplicaciones. Entre los escenarios de implementación habituales se encuentran las aplicaciones que se ejecutan en varias instancias de máquina virtual. Las instancias de máquina virtual están representadas por un equilibrador de carga que ayuda a distribuir el tráfico de red a las diversas instancias.

![NIC en una sola máquina virtual](./media/resource-groups-networking/figure8.png)

| Propiedad | Descripción |
|---|---|
| *frontendIPConfigurations* | un Equilibrador de carga puede incluir una o varias direcciones IP de front-end, también conocidas como IP virtuales (VIP). Estas direcciones IP sirven como entrada para el tráfico y pueden ser de IP pública o privada |
|*backendAddressPools* | se trata de direcciones IP asociadas a las NIC de la máquina virtual a la que se distribuirá la carga |
|*loadBalancingRules* | una propiedad de regla asigna una combinación dada de dirección IP de front-end y puerto, a un conjunto de combinaciones de direcciones IP de back-end y puerto. Con una única definición de un recurso de equilibrador de carga, puede definir varias reglas de equilibrio de carga, donde cada regla refleja una combinación de dirección IP de front-end y puerto y de dirección IP de back-end y puerto asociada con las máquinas virtuales. La regla es un puerto en el grupo de front-end para muchas máquinas virtuales en el grupo de back-end |  
| *Sondeos* | los sondeos le permiten realizar un seguimiento del estado de las instancias de máquina virtual. Si se produce un error en un sondeo de estado, la instancia de la máquina virtual se sacará automáticamente de la rotación |
| *inboundNatRules* | las reglas NAT definen el tráfico entrante que fluye a través de la dirección IP de front-end y se distribuye a la dirección IP de back-end hacia una instancia de máquina virtual específica. La regla NAT es un puerto en el grupo de front-end para una máquina virtual en el grupo de back-end | 

Ejemplo de plantilla de equilibrador de carga en formato Json:

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "dnsNameforLBIP": {
	      "type": "string",
	      "metadata": {
	        "description": "Unique DNS name"
	      }
	    },
	    "location": {
	      "type": "string",
	      "allowedValues": [
	        "East US",
	        "West US",
	        "West Europe",
	        "East Asia",
	        "Southeast Asia"
	      ],
	      "metadata": {
	        "description": "Location to deploy"
	      }
	    },
	    "addressPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/16",
	      "metadata": {
	        "description": "Address Prefix"
	      }
	    },
	    "subnetPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/24",
	      "metadata": {
	        "description": "Subnet Prefix"
	      }
	    },
	    "publicIPAddressType": {
	      "type": "string",
	      "defaultValue": "Dynamic",
	      "allowedValues": [
	        "Dynamic",
	        "Static"
	      ],
	      "metadata": {
	        "description": "Public IP type"
	      }
	    }
	  },
	  "variables": {
	    "virtualNetworkName": "virtualNetwork1",
	    "publicIPAddressName": "publicIp1",
	    "subnetName": "subnet1",
	    "loadBalancerName": "loadBalancer1",
	    "nicName": "networkInterface1",
	    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
	    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
	    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	    "nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]",
	    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	    "backEndIPConfigID": "[concat(variables('nicId'),'/ipConfigurations/ipconfig1')]"
	  },
	  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameforLBIP')]"
        }
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[parameters('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnetRef')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(variables('lbID'), '/backendAddressPools/LoadBalancerBackend')]"
                }
              ],
              "loadBalancerInboundNatRules": [
                {
                  "id": "[concat(variables('lbID'),'/inboundNatRules/RDP')]"
                }
              ]
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[variables('publicIPAddressID')]"
              }
            }
          }
        ],
        "backendAddressPools": [
          {
            "name": "loadBalancerBackEnd"
          }
        ],
        "inboundNatRules": [
          {
            "name": "RDP",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPort": 3389,
              "backendPort": 3389,
              "enableFloatingIP": false
            }
          }
        ]
      }
    }
	  ]
	}

### Recursos adicionales

Lea el artículo [API de REST del Equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx) para obtener más información.

<!---HONumber=AcomDC_1223_2015-->