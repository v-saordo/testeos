#Configuración de un nombre de dominio personalizado para un sitio web de Azure.

Cuando se crea un sitio web, Azure proporciona un subdominio descriptivo en el dominio azurewebsites.net para que los usuarios puedan tener acceso al sitio web con una dirección URL como http://&lt;mysite>.azurewebsites.net. Sin embargo, si configura los sitios web para el modo Compartido o Estándar, puede asignar el sitio web a su propio nombre de dominio.

También puede utilizar el Administrador de tráfico de Azure para equilibrar la carga del tráfico entrante en su sitio web. Para obtener más información acerca de cómo trabaja el Administrador de tráfico con Sitios web, consulte [Control del tráfico de Sitios web Azure con el Administrador de tráfico de Azure][trafficmanager].

> [AZURE.NOTE]Los procedimientos de esta tarea se aplican a Sitios web Azure; para los Servicios en la nube, consulte <a href="/develop/net/common-tasks/custom-dns/">Configuración de un nombre de dominio personalizado en Azure</a>.

> [AZURE.NOTE]Los pasos de esta tarea requieren que configure sus sitios web para el modo estándar o compartido, que puede cambiar la cantidad que se le cobrará por su suscripción. Consulte <a href="/pricing/details/web-sites/">Detalles de precios de Sitios web</a> para obtener más información.

En este artículo:

-   [Descripción de los registros D y CNAME](#understanding-records)
-   [Configuración de los sitios web para el modo compartido o estándar](#bkmk_configsharedmode)
-   [Incorporación de sus sitios web al Administrador de tráfico](#trafficmanager)
-   [Incorporación de un CNAME para el dominio personalizado](#bkmk_configurecname)
-   [Incorporación de un registro D para el dominio personalizado](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Descripción de los registros D y CNAME</h2>

Los registros D y CNAME (o registros de alias) le permiten asociar un nombre de dominio a un sitio web. Sin embargo, cada uno funciona de manera distinta.

###Registro de CNAME o Alias

Un registro CNAME asigna un dominio *específico*, como **contoso.com** o **www.contoso.com**, a un nombre de dominio canónico. En este caso, el nombre de dominio canónico es el nombre de dominio **&lt;myapp>.azurewebsites.net** del sitio web Azure o el nombre de dominio **&lt;myapp>.trafficmgr.com** del perfil del Administrador de tráfico. Una vez creado, CNAME genera un alias para el nombre de dominio **&lt;myapp>.azurewebsites.net** o **&lt;myapp>.trafficmgr.com**. La entrada de CNAME se resolverá en la dirección IP del nombre de dominio **&lt;myapp>.azurewebsites.net** o **&lt;myapp>.trafficmgr.com** automáticamente, por lo que si la dirección IP del sitio web cambia, no tiene que realizar ninguna acción.

> [AZURE.NOTE]Algunos registradores de dominio solo permiten asignar subdominios cuando se utiliza un registro CNAME, como www.contoso.com, no nombres de raíz, como contoso.com. Para obtener más información acerca de los registros CNAME, consulte la documentación que proporciona el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia sobre el registro CNAME</a> o el documento <a href="http://tools.ietf.org/html/rfc1035">Nombres de dominio IETF: implementación y especificación (en inglés)</a>.

###Registro D

El registro D asigna un dominio, como **contoso.com** o **www.contoso.com**, *o un nombre de dominio con comodín* como ***.contoso.com**, a una dirección IP. En el caso de un sitio web de Azure, la IP virtual del servicio o una dirección IP específica que haya adquirido para el sitio web. Por lo tanto, el principal beneficio de un registro D en relación con un registro CNAME es que puede disponer de una entrada que use un carácter comodín, como ****.contoso.com**, que administraría las solicitudes de varios subdominios como **mail.contoso.com**, **login.contoso.com** o **www.contso.com**.

> [AZURE.NOTE]Puesto que un registro D se asigna a una dirección IP estática, no puede resolver automáticamente cambios en la dirección IP de su sitio web. Se proporciona una dirección IP para que se use con registros D cuando establezca la configuración del nombre de dominio personalizado para su sitio web; sin embargo, este valor podría cambiar si elimina y vuelve a crear su sitio web o cambia el modo del sitio web para que vuelva a ser gratuito.

> [AZURE.NOTE]Los registros D no se pueden utilizar para el equilibrio de carga con el Administrador de tráfico. Para obtener más información, consulte [Control del tráfico de Sitios web Azure con el Administrador de tráfico de Azure][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configuración de los sitios web para el modo compartido o estándar</h2>

La configuración de un nombre de dominio personalizado en un sitio web solo está disponible para los modos estándar y compartido para los Sitios web Azure. Antes de cambiar un sitio web del modo gratuito al modo estándar o compartido, primero debe quitar los límites de gasto del sitio vigentes para la suscripción del sitio web. Para obtener más información acerca de los precios de los modos estándar y compartido, consulte la [Información sobre el precio][PricingDetails].

1. En el explorador, abra el [Portal de administración][portal].
2. En la pestaña **Sitios web**, haga clic en el nombre del sitio.

	![][standardmode1]

3. Haga clic en la pestaña **ESCALAR**.

	![][standardmode2]


4. En la sección **general**, defina el modo del sitio web; para ello, haga clic en **COMPARTIDO**.

	![][standardmode3]

	> [AZURE.NOTE]Si va a utilizar el Administrador de tráfico con este sitio web, debe seleccionar el modo estándar en lugar del compartido.

5. Haga clic en **Guardar**.
6. Cuando se le solicite aumentar el coste para el modo compartido (o para el modo estándar, si fue ese el que seleccionó), haga clic en **Sí** si está de acuerdo.

	<!--![][standardmode4]-->**Nota**<br /> Si recibe un error del tipo "Error al configurar escala del sitio web 'nombre del sitio web'", puede usar el botón de detalles para obtener más información.

<a name="trafficmanager"></a><h2>(Opcional) Incorporación de sus sitios web al Administrador de tráfico</h2>

Si desea utilizar su sitio web con el Administrador de tráfico, siga estos pasos.

1. Si todavía no tiene un perfil de Administrador de tráfico, utilice la información en [Crear un perfil de Administrador de tráfico mediante Creación rápida][createprofile] para crear uno. Anote el nombre de dominio **.trafficmgr.com** asociado a su perfil de Administrador de tráfico. Se utilizará en otro paso más adelante.

2. Use la información en [Adición o eliminación de extremos][addendpoint] para agregar el sitio web como un extremo en el perfil del Administrador de tráfico.

	> [AZURE.NOTE]Si su sitio web no aparece en la lista al agregar un extremo, compruebe que está configurado en modo estándar. Si desea trabajar con el Administrador de tráfico, debe utilizar el modo estándar para su sitio web.

3. Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

4. Ahora busque el lugar en el que puede seleccionar o especificar los registros CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdominios**.

5. También debe proporcionar el alias del dominio o del subdominio para CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**.

5. Debe proporcionar un nombre de host que sea el nombre de dominio canónico para ese alias de CNAME. Este es el nombre **.trafficmgr.com** de su sitio web.

Por ejemplo, el siguiente registro CNAME desvía todo el tráfico de **www.contoso.com** a **contoso.trafficmgr.com**, el nombre de dominio de un sitio web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nombre de host/Subdominio</strong></td>
<td><strong>Dominio canónico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

Los visitantes de **www.contoso.com** no verán nunca el verdadero host (contoso.azurewebsite.net), por lo que el usuario final no percibirá el proceso de desvío.

> [AZURE.NOTE]Si usa el Administrador de tráfico con un sitio web, no es preciso que siga los pasos de las secciones siguientes, "**Adición de un CNAME a un dominio personalizado**" y "**Adición de un registro D a un dominio personalizado**". El registro CNAME creado en los pasos anteriores dirigirá el tráfico entrante al Administrador de tráfico, quien entonces dirigirá el tráfico a los extremos del sitio web.

<a name="bkmk_configurecname"></a><h2>Incorporación de un CNAME para el dominio personalizado</h2>

Para crear un registro CNAME, debe agregar una nueva entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro CNAME. Sin embargo, los conceptos son los mismos.

1. Use uno de estos métodos para buscar el nombre de dominio **.azurewebsite.net** asignado a su sitio web.

	* Inicie sesión en el [Portal de administración de Azure][portal], seleccione el sitio web, seleccione **Panel** y, a continuación, busque la entrada **Dirección URL del sitio** del sitio en la sección de **vista rápida**.

	* Instale y configure [Azure Powershell](/manage/install-and-configure-windows-powershell/) y, a continuación, use el siguiente comando:

			get-azurewebsite yoursitename | select hostnames

	* Instale y configure la [Interfaz de la línea de comandos de Azure](/manage/install-and-configure-cli/) y, a continuación, use el siguiente comando:

			azure site domain list yoursitename

	Guarde este nombre **.azurewebsite.net**, puesto que se utilizará en los pasos siguientes.

3. Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

4. Ahora busque el lugar en el que puede seleccionar o especificar los registros CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada. Debe buscar las palabras **CNAME**, **Alias** o **Subdominios**.

5. También debe proporcionar el alias del dominio o del subdominio para CNAME. Por ejemplo, **www** si desea crear un alias para **www.customdomain.com**. Si desea crear un alias para el dominio raíz, puede enumerarse como el símbolo "**@**" en las herramientas de DNS del registrador.

5. Debe proporcionar un nombre de host que sea el nombre de dominio canónico para ese alias de CNAME. Este es el nombre **.azurewebsite.net** de su sitio web.

Por ejemplo, el siguiente registro CNAME desvía todo el tráfico de **www.contoso.com** a **contoso.azurewebsite.net**, el nombre de dominio de un sitio web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nombre de host/Subdominio</strong></td>
<td><strong>Dominio canónico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

Los visitantes de **www.contoso.com** no verán nunca el verdadero host (contoso.azurewebsite.net), por lo que el usuario final no percibirá el proceso de desvío.

> [AZURE.NOTE]El ejemplo anterior solo se aplica al tráfico en el subdominio __www__. Puesto que no puede usar caracteres comodín con registros CNAME, debe crear un CNAME para cada dominio/subdominio. Si desea dirigir el tráfico desde subdominios, como *.contoso.com, a su dirección azurewebsite.net, puede configurar una entrada __Redirección de URL__ o __Desvío de URL__ en la configuración de DNS o crear un registro D.

> [AZURE.NOTE]El CNAME puede tardar un tiempo en propagarse por el sistema DNS. No puede establecer el CNAME para el sitio web hasta que el CNAME se haya propagado. Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el CNAME está disponible.

###Incorporación del nombre de dominio a su sitio web

Una vez que se haya propagado el registro CNAME para el nombre de dominio, debe asociarlo a su sitio web. Puede agregar el nombre de dominio personalizado definido por el registro CNAME a su sitio web mediante la Interfaz de la línea de comandos de Azure (CLI de Azure) o con el Portal de administración de Azure.

**Para agregar un nombre de dominio con las herramientas de la línea de comandos**

Instale y configure la [Interfaz de la línea de comandos de Azure](/manage/install-and-configure-cli/) y, a continuación, use el siguiente comando:

	azure site domain add customdomain yoursitename

Por ejemplo, con el siguiente ejemplo se agregará un nombre de dominio personalizado de **www.contoso.com** al sitio web **contoso.azurewebsite.net**:

	azure site domain add www.contoso.com contoso

Puede confirmar que el nombre de dominio personalizado se agregó al sitio web mediante el siguiente comando:

	azure site domain list yoursitename

La lista que devuelve este comando debe contener el nombre de dominio personalizado y la entrada **.azurewebsite.net** predeterminada.

**Para agregar un nombre de dominio con el Portal de administración de Azure**

1. En el explorador, abra el [Portal de administración de Azure][portal].

2. En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** en la parte inferior de la página.

	![][setcname2]

6. En el cuadro de texto **NOMBRES DE DOMINIO**, escriba el nombre de dominio que ha configurado.

	![][setcname3]

6. Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección de **nombres de dominio** de la página **Configurar** del sitio web.

<a name="bkmk_configurearecord"></a><h2>Incorporación de un registro D para el dominio personalizado</h2>

Para crear un registro D, primero debe buscar la dirección IP de su sitio web. A continuación, agregue una entrada en la tabla DNS para el dominio personalizado mediante las herramientas proporcionadas por el registrador. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro D. Sin embargo, los conceptos son los mismos. Además de crear un registro D, debe crear también un registro CNAME que Azure utilizará para comprobar el registro D.

1. En el explorador, abra el [Portal de administración de Azure][portal].

2. En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** desde la parte inferior de la pantalla.

	![][setcname2]

5. En el cuadro de diálogo **Administrar dominios personalizados**, busque **La dirección IP que se debe usar al configurar registros D**. Copie la dirección IP. Esta se usará cuando se cree el registro D.

5. En el cuadro de diálogo **Administrar dominios personalizados**, escribe el nombre de dominio awverify al final del texto en la parte superior del cuadro de diálogo. Debe ser **awverify.mysite.azurewebsites.net**, donde **mysite** es el nombre de su sitio web. Copie esto, ya que es el nombre de dominio usado cuando se crea el registro CNAME de comprobación.

6. Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Busque los vínculos o áreas del sitio etiquetados como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

6. Busque el lugar en el que puede seleccionar o especificar los registros D y CNAME. Es posible que tenga que seleccionar el tipo de registro de un menú desplegable o ir a una página de configuración avanzada.

7. Realice los siguientes pasos para crear el registro D:

	1. Seleccione o especifique el dominio o subdominio que usará el registro D. Por ejemplo, seleccione **www** si desea crear un alias para **www.customdomain.com**. Si desea crear una entrada de comodín para todos los subdominios, especifique '\_\_*\_\_'. De esta forma, se incluirán todos los subdominios como **mail.customdomain.com**, **login.customdomain.com** y **www.customdomain.com**.

		Si desea crear un registro D para el dominio raíz, puede enumerarse como el símbolo "**@**" en las herramientas DNS del registrador.

	2. Especifique la dirección IP del servicio en la nube en el campo proporcionado. De esta forma, se asocia la entrada del dominio utilizada en el registro D a la dirección IP de la implementación del servicio en la nube.

		Por ejemplo, el siguiente registro D desvía todo el tráfico de **contoso.com** a **137.135.70.239**, la dirección IP de nuestra aplicación implementada:

		<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nombre de host/subdominio</strong></td>
<td><strong>Dirección IP</strong></td>
</tr>
<tr>
<td>@</td>
<td>137.135.70.239</td>
</tr>
</table>En este ejemplo se crea un registro D para el dominio raíz. Si desea crear una entrada de comodín para incluir todos los subdominios, debe especificar '\_\_*\_\_' como subdominio.

7. A continuación, cree un registro CNAME que cuente con un alias de **awverify**, y un dominio canónico de **awverify.mysite.azurewebsites.net** obtenido anteriormente.

	> [AZURE.NOTE]Es posible que un alias de awverify funcione con algunos registradores, pero puede que algunos requieran el nombre de dominio del alias completo de awverify.www.customdomainname.com o awverify.customdomainname.com.

	Por ejemplo, a continuación se crea un registro CNAME que Azure puede usar para comprobar la configuración del registro D.

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nombre de host/Subdominio</strong></td>
<td><strong>Dominio canónico</strong></td>
</tr>
<tr>
<td>awverify</td>
<td>awverify.contoso.azurewebsites.net</td>
</tr>
</table>

> [AZURE.NOTE]El CNAME awverify tardará cierto tiempo en propagarse por el sistema DNS. No puede establecer el nombre de dominio personalizado definido por el registro D para el sitio web hasta que se haya propagado el CNAME de awverify. Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el CNAME está disponible.

###Incorporación del nombre de dominio a su sitio web

Una vez que se haya propagado el registro CNAME de **awverify** para el nombre de dominio, puede asociar el dominio personalizado definido por el registro D al sitio web. Puede agregar el nombre de dominio personalizado definido por el registro D a su sitio web mediante la CLI de Azure o con el Portal de administración de Azure.

**Para agregar un nombre de dominio con la Interfaz de la línea de comandos de Azure (CLI de Azure)**

Instale y configure la [CLI de Azure](/manage/install-and-configure-cli/) y, a continuación, use el siguiente comando:

	azure site domain add customdomain yoursitename

Por ejemplo, con el siguiente ejemplo se agregará un nombre de dominio personalizado **contoso.com** al sitio web **contoso.azurewebsite.net**:

	azure site domain add contoso.com contoso

Puede confirmar que el nombre de dominio personalizado se agregó al sitio web mediante el siguiente comando:

	azure site domain list yoursitename

La lista que devuelve este comando debe contener el nombre de dominio personalizado y la entrada **.azurewebsite.net** predeterminada.

**Para agregar un nombre de dominio con el Portal de administración de Azure**

1. En el explorador, abra el [Portal de administración de Azure][portal].

2. En la pestaña **Sitios web**, haga clic en el nombre del sitio, seleccione **Panel** y, a continuación, seleccione **Administrar dominios** en la parte inferior de la página.

	![][setcname2]

6. En el cuadro de texto **NOMBRES DE DOMINIO**, escriba el nombre de dominio que ha configurado.

	![][setcname3]

6. Haga clic en la marca de verificación para aceptar el nombre de dominio.

Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección de **nombres de dominio** de la página **Configurar** del sitio web.

> [AZURE.NOTE]Una vez que haya agregado el nombre de dominio personalizado definido por el registro D a su sitio web, puede quitar el registro CNAME de awverify mediante las herramientas proporcionadas por el registrador. Sin embargo, si desea agregar otro registro D más adelante, tendrá que volver a crear el registro awverify para poder asociar el nuevo nombre de dominio definido por el nuevo registro D al sitio web.

## Pasos siguientes

-   [Administración de sitios web](/manage/services/web-sites/how-to-manage-websites/)

-   [Configuración de un certificado SSL para Sitios web](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]: #bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png

<!---HONumber=Oct15_HO3-->