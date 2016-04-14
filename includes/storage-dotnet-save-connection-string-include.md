### Guardar las credenciales de la cuenta de almacenamiento en el archivo app.config

A continuación, guardará las credenciales en el archivo app.config del proyecto. Edite el archivo app.config de forma que su aspecto sea similar al del siguiente ejemplo, pero reemplace `myaccount` por el nombre de su cuenta de almacenamiento y `mykey` por la clave de su cuenta de almacenamiento.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

<!---HONumber=AcomDC_0309_2016-->