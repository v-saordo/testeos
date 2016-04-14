## Dirección IP pública
Un recurso de dirección IP pública le proporciona una dirección IP de Internet reservada o dinámica. Aunque puede crear una dirección IP pública como un objeto independiente, debe asociarlo a otro objeto para usar la dirección realmente. Puede asociar una dirección IP pública a un equilibrador de carga, puerta de enlace de aplicaciones o una NIC para proporcionar acceso a Internet a esos recursos.

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**publicIPAllocationMethod**|Define si la dirección IP es *estática* o *dinámica*.|estática, dinámica|
|**idleTimeoutInMinutes**|Define el tiempo de espera de inactividad, con un valor predeterminado de 4 minutos. Si no se reciben más paquetes para una sesión dada dentro de este tiempo, se termina la sesión.|cualquier valor entre 4 y 30|
|**ipAddress**|Dirección IP asignada al objeto. Se trata de una propiedad de solo lectura.|104\.42.233.77|

### Configuración de DNS
Las direcciones IP públicas tienen un objeto secundario denominado **dnsSettings** que contiene las propiedades siguientes:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**domainNameLabel**|Nombre de host usado para la resolución de nombres.|www, ftp, vm1|
|**fqdn**|Nombre completo de la dirección IP pública.|www.westus.cloudapp.azure.com|
|**reverseFqdn**|Nombre de dominio completo que se resuelve como la dirección IP y está registrado en DNS como un registro PTR.|www.contoso.com.|

Dirección IP pública de ejemplo en formato JSON:

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
			 "fqdn": "mylabel.westus.cloudapp.azure.com",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

### Recursos adicionales

- Obtener más información sobre [direcciones IP públicas](virtual-networks-reserved-public-ip.md).
- Obtenga más información sobre las [direcciones IP públicas de nivel de instancia](virtual-networks-instance-level-public-ip.md).
- Lea la [documentación de referencia de API de REST](https://msdn.microsoft.com/library/azure/mt163638.aspx) para obtener información sobre direcciones IP públicas.

<!---HONumber=Oct15_HO3-->