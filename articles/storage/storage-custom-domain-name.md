<properties
	pageTitle="Configurar un nombre de dominio para el punto de conexión de Almacenamiento de blobs | Microsoft Azure"
	description="Obtenga información sobre cómo asignar un dominio de usuario personalizado para el punto de conexión de Almacenamiento de blobs para una cuenta de almacenamiento de Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/14/2016"
	ms.author="tamram"/>


# Configurar un nombre de dominio personalizado para el punto de conexión de Almacenamiento de blobs

## Información general

Puede configurar un dominio personalizado para obtener acceso a los datos Blob en la cuenta de almacenamiento de Azure. El punto de conexión predeterminado para Almacenamiento de blobs es https://<*mystorageaccount*>.blob.core.windows.net. Si asigna un subdominio y un dominio personalizado, como **www.contoso.com**, al extremo del blob para la cuenta de almacenamiento, los usuarios también pueden obtener acceso a los datos Blob en la cuenta de almacenamiento a través de ese dominio.


> [AZURE.NOTE]	Los procedimientos de esta tarea se aplican a las cuentas de almacenamiento de Azure. Para obtener la información relativa a los Servicios en la nube, consulte <a href = "/develop/net/common-tasks/custom-dns/">Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure</a>; para Sitios web, consulte <a href="/develop/net/common-tasks/custom-dns-web-site/">Configuración de un nombre de dominio personalizado en Servicio de aplicaciones de Azure</a>.

Existen dos formas de que el dominio personalizado apunte al extremo del blob para la cuenta de almacenamiento. La forma más simple es crear un registro CNAME asignando el dominio y el subdominio personalizados al extremo del blob. Un registro CNAME es una característica DNS que asigna un dominio de origen a un dominio de destino. En este caso, el dominio de origen es el dominio y el subdominio personalizados. Tenga en cuenta que el subdominio es siempre obligatorio. El dominio de destino es el extremo de servicio del blob.

El proceso de asignación del dominio personalizado al punto de conexión del blob puede provocar un período breve de inactividad para el dominio mientras registra el dominio en el [Portal de Azure clásico](https://manage.windowsazure.com). Si el dominio personalizado ya es compatible con una aplicación con un contrato de nivel de servicio (SLA) que requiere que no exista tiempo de inactividad, puede usar el subdominio **asverify** de Azure para proporcionar un paso de registro intermedio para que los usuarios puedan obtener acceso al dominio mientras se realiza la asignación de DNS.

La siguiente tabla muestra las direcciones URL de ejemplo para obtener acceso a los datos Blob en una cuenta de almacenamiento denominada **mystorageaccount**. El dominio personalizado registrado para la cuenta de almacenamiento es **www.contoso.com**:

Tipo de recurso|Formatos de dirección URL
---|---
Cuenta de almacenamiento|**Dirección URL predeterminada:** http://mystorageaccount.blob.core.windows.net<p>**URL de dominio personalizada:** http://www.contoso.com</td>
Blob|**Dirección URL predeterminada:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL de dominio personalizada:**http://www.contoso.com/mycontainer/myblob
Contenedor raíz|**Dirección URL predeterminada:** http://mystorageaccount.blob.core.windows.net/myblob o http://mystorageaccount.blob.core.windows.net/$root/myblob<p>**URL de dominio personalizada:** http://www.contoso.com/myblob o http://www.contoso.com/$root/myblob

## Registro de un dominio personalizado para la cuenta de almacenamiento

Use este procedimiento para registrar el dominio personalizado si no le preocupa que el dominio deje de estar disponible brevemente para los usuarios o si el dominio personalizado no hospeda actualmente una aplicación.

Si el dominio personalizado es compatible actualmente con una aplicación que no puede tener ningún tiempo de inactividad, use el procedimiento descrito en <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">Registro de un dominio personalizado para la cuenta de almacenamiento con el subdominio asverify del intermediario</a>.

Para configurar un nombre de dominio personalizado, debe crear un nuevo registro CNAME con el registrador de dominios. El registro CNAME especifica un alias para el nombre de dominio; en este caso, asigna la dirección del dominio personalizado al punto de conexión de Almacenamiento de blobs para la cuenta de almacenamiento.

Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro CNAME. Sin embargo, el concepto es el mismo. Tenga en cuenta que muchos paquetes de registro de domino básicos no ofrecen configuración DNS, por lo que puede que sea necesario actualizar el paquete de registro de dominios antes de crear el registro CNAME.

1.  En el [Portal de Azure clásico](https://manage.windowsazure.com), diríjase a la pestaña **Almacenamiento**.

2.  En la pestaña **Almacenamiento**, haga clic en el nombre de la cuenta de almacenamiento para la que desee asignar el dominio personalizado.

3.  Haga clic en la pestaña **Configure**.

4.  En la parte inferior de la pantalla, haga clic en **Administrar dominio** para mostrar el cuadro de diálogo **Administrar dominio personalizado**. En el texto de la parte superior del cuadro de diálogo, verá información sobre cómo crear el registro CNAME. Para este procedimiento, ignore el texto que hace referencia al subdominio **asverify**.

5.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

6.  Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras **CNAME**, **Alias** o **Subdomains**.

7.  Cree un nuevo registro CNAME y proporcione un alias de subdominio como **www** o **photos**. A continuación, proporcione un nombre de host, que es el extremo del servicio BLOB, en el formato **mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de la cuenta de almacenamiento). El nombre de host que se va a utilizar se proporciona en el texto del cuadro de diálogo **Manage Custom Domain**.

8.  Una vez que haya creado el registro CNAME, vuelva al cuadro de diálogo **Manage Custom Domain** y especifique el nombre del dominio personalizado, incluido el subdominio, en el campo **Nombre de dominio personalizado**. Por ejemplo, si el dominio es **contoso.com** y el subdominio **www**, especifique **www.contoso.com**; si el subdominio es **photos**, especifique **photos.contoso.com**. Tenga en cuenta que el subdominio es obligatorio.

9. Haga clic en el botón **Register** para registrar el dominio personalizado.

	Si el registro es correcto, verá el mensaje **Your custom domain is active**. Los usuarios podrán ahora ver los datos Blob en el dominio personalizado siempre que dispongan de los permisos correspondientes.

## Registro de un dominio personalizado para la cuenta de almacenamiento con el subdominio asverify del intermediario

Use este procedimiento para registrar el dominio personalizado si este es actualmente compatible con una aplicación con un contrato de nivel de servicio que requiera que no exista tiempo de inactividad. Mediante la creación de un CNAME que apunte desde asverify.&lt;subdomain&gt;.&lt;customdomain&gt; a asverify.&lt;storageaccount&gt;.blob.core.windows.net, puede registrar previamente su dominio con Azure. A continuación, puede crear un segundo CNAME que señale de &lt;subdomain&gt;.&lt;customdomain&gt; a &lt;storageaccount&gt;.blob.core.windows.net, en el que el tráfico directo al dominio personalizado se dirigirá al extremo del blob.

El subdominio asverify es un subdominio especial reconocido por Azure. Si antepone **asverify** a su propio subdominio, permite que Azure reconozca el dominio personalizado sin modificar el registro DNS para el dominio. Una vez que modifique el registro DNS para el dominio, se asignará al extremo del blob sin que exista tiempo de inactividad.

1.  En el [Portal de Azure clásico](https://manage.windowsazure.com), diríjase a la pestaña **Almacenamiento**.

2.  En la pestaña **Almacenamiento**, haga clic en el nombre de la cuenta de almacenamiento para la que desee asignar el dominio personalizado.

3.  Haga clic en la pestaña **Configure**.

4.  En la parte inferior de la pantalla, haga clic en **Manage Domain** para visualizar el cuadro de diálogo **Manage Custom Domain**. En el texto de la parte superior del cuadro de diálogo, verá información sobre cómo crear el registro CNAME con el subdominio **asverify**.

5.  Inicie sesión en el sitio web del registrador DNS y vaya a la página de administración de DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

6.  Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras **CNAME**, **Alias** o **Subdomains**.

7.  Cree un nuevo registro CNAME y proporcione un alias de subdominio que incluya el subdominio asverify. Por ejemplo, el subdominio que especifique estará en formato **asverify.www** o **asverify.photos**. A continuación, proporcione un nombre de host, que es el extremo del servicio Blob, en el formato **asverify.mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de la cuenta de almacenamiento). El nombre de host que se va a utilizar se proporciona en el texto del cuadro de diálogo **Manage Custom Domain**.

8.  Una vez que haya creado el registro CNAME, vuelva al cuadro de diálogo **Manage Custom Domain** y especifique el nombre del dominio personalizado en el campo **Nombre de dominio personalizado**. Por ejemplo, si el dominio es **contoso.com** y el subdominio **www**, especifique **www.contoso.com**; si el subdominio es **photos**, especifique **photos.contoso.com**. Tenga en cuenta que el subdominio es obligatorio.

9.	Haga clic en la casilla que dice **Avanzado: Uso del subdominio asverify para registrar previamente mi dominio personalizado**.

10. Haga clic en el botón **Register** para registrar previamente el dominio personalizado.

	Si el registro previo es correcto, verá el mensaje **Your custom domain is active**.

11. En este momento, Azure ha comprobado el dominio personalizado, pero no se ha realizado el enrutamiento del tráfico al dominio en la cuenta de almacenamiento. Para completar el proceso, vuelva al sitio web del registrador DNS y cree otro registro CNAME que asigne su subdominio al extremo del servicio BLOB. Por ejemplo, especifique el subdominio como **www** o **photos**, y el nombre de host como **mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de la cuenta de almacenamiento). Con este paso se completa el registro del dominio personalizado.

12. Finalmente, puede eliminar el registro CNAME que creó con **asverify**, ya que solo era necesario como paso intermedio.

Los usuarios podrán ahora ver los datos Blob en el dominio personalizado siempre que dispongan de los permisos correspondientes.

## Comprobación de que el dominio personalizado hace referencia al extremo del servicio BLOB

Para comprobar que el dominio personalizado se haya asignado realmente al extremo del servicio BLOB, cree un blob en un contenedor público en la cuenta de almacenamiento. A continuación, en un explorador web, use un URI con el mismo formato para obtener acceso al blob:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Por ejemplo, puede usar el siguiente URI para obtener acceso a un formulario web a través de un subdominio personalizado **photos.contoso.com** que se asigne a un blob en el contenedor **myforms**:

-   http://photos.contoso.com/myforms/applicationform.htm

## Recursos adicionales

-   [Cómo asignar un dominio personalizado al punto de conexión de la Red de entrega de contenido (CDN)](../cdn/cdn-map-content-to-custom-domain.md)

<!---HONumber=AcomDC_0218_2016-->