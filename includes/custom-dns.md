# Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure

> [AZURE.NOTE]
> Póngase en marcha más rápido: use el NUEVO [tutorial guiado](http://support.microsoft.com/kb/2990804) de Azure.  Con este tutorial, asociar un nombre de dominio personalizado y proteger las comunicaciones (SSL) con los Servicios en la nube de Azure o Sitios web Azure es facilísimo.

Cuando se crea una aplicación en Azure, Azure proporciona un subdominio en el dominio cloudapp.net para que los usuarios puedan obtener acceso a la aplicación en una dirección URL como http://&lt;*miaplicacion*>.cloudapp.net. Sin embargo, también puede exponer su aplicación en su propio nombre de dominio, como contoso.com.

> [AZURE.NOTE] 
> Los procedimientos de esta tarea se aplican a los Servicios en la nube de Azure. Para obtener información sobre las cuentas de almacenamiento, consulte [Configuración de un nombre de dominio personalizado para una cuenta de Almacenamiento de Azure](../articles/storage-custom-domain-name.md). Para obtener información sobre sitios web, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure](../articles/web-sites-custom-domain-name.md).


## Descripción de los registros D y CNAME

Los registros D y CNAME (o registros de alias) le permiten asociar un nombre de dominio a un servidor específico (o, en este caso, servicio). Sin embargo, cada uno funciona de manera distinta. Existen también algunas consideraciones específicas al usar los registros D con los Servicios en la nube de Azure que debe considerar antes de decidir cuál usar.

### Registro de CNAME o Alias

Un registro CNAME asigna un nombre de dominio canónico a un dominio *specific*, como **contoso.com** o **www.contoso.com**. En este caso, el nombre de dominio canónico es el nombre de dominio **&lt;<miaplicación>.cloudapp.net** de su aplicación hospedada de Azure. Una vez creado, el CNAME crea un alias para **&lt;miaplicación>.cloudapp.net**. La entrada de CNAME se resolverá en la dirección IP del servicio de **&lt;<miaplicación>.cloudapp.net** de manera automática, por lo que si la dirección IP del servicio en la nube cambia, no tiene que realizar ninguna acción.

> [AZURE.NOTE] 
> Algunos registradores de dominio solo permiten asignar subdominios cuando se utiliza un registro CNAME, como www.contoso.com y no los nombres raíz, como contoso.com. Para obtener más información acerca de los registros CNAME, consulte la documentación que proporciona el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia sobre el registro CNAME</a> o el documento <a href="http://tools.ietf.org/html/rfc1035">Nombres de dominio IETF: implementación y especificación</a> (en inglés).

### Registro D

Un registro D asigna una dirección IP a un dominio, como **contoso.com** o **www.contoso.com**, *or a wildcard domain* como ***.contoso.com**. En el caso de un Servicio en la nube de Azure, la IP virtual del servicio. Por lo tanto, el principal beneficio de un registro D en relación con un registro CNAME es que puede disponer de una entrada que utilice un carácter comodín, como ***.contoso.com**, que administraría las solicitudes de varios subdominios como **mail.contoso.com**, **, login.contoso.com** o **www.contso.com**.

> [AZURE.NOTE]
> Puesto que un registro D se asigna a una dirección IP estática, no puede resolver automáticamente cambios en la dirección IP de su Servicio en la nube. La dirección IP que usa el Servicio en la nube se asigna la primera vez que se implementa en una ranura vacía (producción o ensayo). Si elimina la implementación para la ranura, Azure libera la dirección IP y a toda implementación posterior en la ranura se le podrá dar una dirección IP nueva.
> 
> De manera conveniente, la dirección IP de una ranura de implementación dada (producción o ensayo) se mantiene cuando se realiza un intercambio entre las implementaciones de ensayo y producción o se realiza una actualización local de una implementación existente. Para obtener más información sobre la realización de estas acciones, consulte [Administración de servicios en la nube](../articles/cloud-services-how-to-manage.md).


## Incorporación de un CNAME al dominio personalizado

Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro CNAME. Sin embargo, los conceptos son los mismos.

1. Use uno de estos métodos para buscar el nombre de dominio **.cloudapp.net** asignado a su servicio en la nube.

  * Inicie sesión en el [Portal de administración de Azure], seleccione su servicio en la nube, seleccione **Panel** y luego busque la entrada **URL de sitio** en la sección **Vista rápida**.

  		  ![sección de vista rápida que muestra la URL][csurl]

  * Instale y configure [Azure Powershell](../articles/install-configure-powershell.md)y, a continuación, utilice el siguiente comando:

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  Guarde el nombre de dominio que se usó en la URL devuelta por cualquier método, ya que la necesitará al crear un registro de CNAME.

1.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

2.  Ahora busque el lugar en el que puede seleccionar o especificar los registros CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdominios**.

3.  También debe proporcionar el alias del dominio o subdominio para el registro CNAME, como **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede ponerse en una lista como el símbolo **@**' en las herramientas de DNS de su registrador.

4. A continuación, debe proporcionar un nombre de host canónico que, en este caso, es el dominio **cloudapp.net** de su aplicación.

Por ejemplo, el siguiente registro CNAME reenvía todo el tráfico de **www.contoso.com** a **contoso.cloudapp.net**, el nombre de dominio personalizado de su aplicación implementada:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nombre de host/Subdominio</strong></td>
<td><strong>Dominio canónico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.cloudapp.net</td>
</tr>
</table>

Un visitante de **www.contoso.com** nunca verá el verdadero host
(contoso.cloudapp.net), por lo que el proceso de reenvío es invisible para el
usuario final.

> [AZURE.NOTE]
> El ejemplo anterior solo se aplica al tráfico en el subdominio <strong>www</strong>. Puesto que no puede usar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio/subdominio. Si desea dirigir el tráfico desde subdominios, como *.contoso.com, a su dirección cloudapp.net, puede configurar una entrada de <strong>Redirección de URL</strong> o de <strong>Reenvío de URL</strong> en la configuración de DNS, o bien crear un registro D.


## Adición de un registro D para el dominio personalizado

Para crear un registro D, primero debe buscar la dirección IP virtual de su servicio en la nube. A continuación, agregue una entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro D. Sin embargo, los conceptos son los mismos.

1. Use uno de los siguientes métodos para obtener la dirección IP de su servicio en la nube.

  * Inicie sesión en el [Portal de administración de Azure], seleccione su servicio en la nube, seleccione **Panel** y luego busque la entrada **Dirección IP virtual pública (VIP)** la sección **Vista rápida**.

   		 ![sección de vista rápida que muestra la IP virtual][vip]

  * Instale y configure [Azure Powershell](../articles/install-configure-powershell.md)y, a continuación, utilice el siguiente comando:

      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip

    Si tiene varios extremos asociados con su servicio en la nube, recibirá varias líneas que contienen la dirección IP, pero todas deben mostrar la misma dirección.

  Guarde la dirección IP, debido a que la necesitará cuando cree un registro D.

1.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

2.  Ahora busque el lugar en el que puede seleccionar o especificar los registros D. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada.

3. Seleccione o especifique el dominio o subdominio que usará el registro D. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de comodín para todos los subdominios, especifique '__*__'. De esta forma, se incluirán todos los subdominios como **mail.customdomain.com****, login.customdomain.com** y **www.customdomain.com**.

  Si desea crear un registro D para el dominio raíz, debe aparecer con un símbolo '**@**' en las herramientas DNS del registrador.

4. Especifique la dirección IP del servicio en la nube en el campo proporcionado. De esta forma, se asocia la entrada del dominio utilizada en el registro D a la dirección IP de la implementación del servicio en la nube.

Por ejemplo, el siguiente registro D desvía todo el tráfico de **contoso.com** a **137.135.70.239**, la dirección IP de su aplicación implementada:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nombre de host/subdominio</strong></td>
<td><strong>Dirección IP</strong></td>
</tr>
<tr>
<td>@</td>
<td>137.135.70.239</td>
</tr>
</table>

En este ejemplo se crea un registro D para el dominio raíz. Si desea crear una entrada de comodín para incluir todos los subdominios, debe especificar '__*__' como subdominio.

## Pasos siguientes

-   [Administración de servicios en la nube](../articles/cloud-services-how-to-manage.md)
-   [Asignación del contenido de la red CDN a un dominio personalizado][]

  [Exposición de una aplicación en un dominio personalizado]: #access-app
  [Adición de un registro CNAME a un dominio personalizado]: #add-cname
  [Exposición de los datos en un dominio personalizado]: #access-data
  [Intercambios de IP virtuales]: http://msdn.microsoft.com/library/ee517253.aspx
  [Creación de un registro CNAME que asocie el subdominio a la cuenta de almacenamiento]: #create-cname
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Validar dominio personalizado, cuadro de diálogo]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
  [Asignación del contenido de la red CDN a un dominio personalizado]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx
  [vip]: ./media/custom-dns/csvip.png
  [csurl]: ./media/custom-dns/csurl.png

<!--HONumber=49-->