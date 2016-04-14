El Sistema de nombres de dominio (DNS) se utiliza para buscar recursos en Internet. Por ejemplo, cuando se escribe la dirección de una aplicación web en el explorador o se hace clic en un vínculo de una página web, utiliza DNS para convertir el dominio en una dirección IP. La dirección IP es algo parecido a la dirección de una calle, pero su sistema es algo complejo. Por ejemplo, es mucho más fácil recordar un nombre DNS como **contoso.com** que recordar una dirección IP como 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

El sistema DNS se basa en *registros*. Los registros asocian un *nombre* específico, como **contoso.com**, a una dirección IP u otro nombre DNS. Cuando una aplicación, como un explorador web, busca un nombre en DNS, encuentra el registro y utiliza aquello a lo que apunte como dirección. Si el valor al que apunta es una dirección IP, el explorador utilizará ese valor. Si apunta a otro nombre DNS, la aplicación tiene que volver a realizar una resolución. En última instancia, todas las resoluciones de nombre terminarán en una dirección IP.

Cuando se crea una aplicación web en el Servicio de aplicaciones, se le asigna automáticamente un nombre DNS. Este nombre adopta la forma de **&lt;nombredeaplicacionweb&gt;.azurewebsites.net**. También hay una dirección IP virtual que se puede usar al crear registros DNS, por lo que puede crear registros que apunten a **.azurewebsites.net**, o bien puede apuntarlos a la dirección IP.

> [AZURE.NOTE]La dirección IP de la aplicación web cambiará si elimina y vuelve a crear dicha aplicación, o si cambia el modo de plan a **Gratuito** después de haberlo establecido en **Básico**, **Compartido** o **Estándar**.

También hay varios tipos de registros, cada uno con sus propias funciones y limitaciones, pero para las aplicaciones web, solo nos interesan dos, los registros *D* y *CNAME*.

###Registro de dirección (registro D)

El registro D asigna un dominio, como **contoso.com** o **www.contoso.com**, *o un nombre de dominio con comodín* como ***.contoso.com**, a una dirección IP. En el caso de una aplicación web del Servicio de aplicaciones, la IP virtual del servicio o una dirección IP específica que haya adquirido para su aplicación web.

Las principales ventajas de un registro D sobre un registro CNAME son:

* Puede asignar un dominio raíz, como **contoso.com**, a una dirección IP; muchos registradores solo lo permiten con los registros D.

* Puede disponer de una entrada que utilice un carácter comodín, como ***.contoso.com**, que administraría las solicitudes de varios subdominios como **mail.contoso.com**, **blogs.contoso.com** o **www.contso.com**.

> [AZURE.NOTE]Dado que cada registro D se asigna a una dirección IP estática, no puede resolver automáticamente los cambios que se produzcan en la dirección IP de la aplicación web. Al configurar las opciones del nombre de dominio personalizado para la aplicación web se proporciona una dirección IP que se puede usar con los registros D; sin embargo, este valor puede cambiar si la aplicación web se elimina y se vuelve a crear, o si se cambia el nodo de plan del Servicio de aplicaciones de nuevo a **Gratuito**.

###Registro de alias (registro CNAME)

Un registro CNAME asigna un nombre DNS *específico*, como **mail.contoso.com** o **www.contoso.com**, a otro nombre de dominio (canónico). En el caso de las Aplicaciones web del Servicio de aplicaciones, el nombre de dominio canónico es el nombre de dominio **&lt;nombredeaplicacionweb>.azurewebsites.net** de su aplicación web. Una vez creado, el CNAME crea un alias para el nombre de dominio **&lt;nombredeaplicacionweb>.azurewebsites.net**. La entrada de CNAME se resolverá automáticamente en la dirección IP del servicio del nombre de dominio **&lt;nombredeaplicacionweb>.azurewebsites.net**, por lo que si la dirección IP de la aplicación web cambia, no es preciso realizar ninguna acción.

> [AZURE.NOTE]Algunos registradores de dominio solo permiten asignar subdominios cuando se utiliza un registro CNAME, como **www.contoso.com**, no nombres de raíz, como **contoso.com**. Para obtener más información acerca de los registros CNAME, consulte la documentación que proporciona el registrador, <a href="http://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia sobre el registro CNAME</a> o el documento <a href="http://tools.ietf.org/html/rfc1035">Nombres de dominio IETF: implementación y especificación (en inglés)</a>.

###Detalles de DNS de aplicación web

El uso de un registro D con Aplicaciones web requiere que antes se cree uno de los siguientes registros CNAME:

* **Para el dominio raíz o subdominios comodín**: un nombre DNS de **awverify** a **awverify.&lt;nombredeaplicacionweb&gt;.azurewebsites.net**.

* **Para un subdominio específico**: un nombre DNS de **awverify.&lt;subdominio>** a **awverify.&lt;nombredeaplicacionweb.azurewebsites.net**. Por ejemplo, **awverify.blogs** si el registro D es para **blogs.contoso.com**.

Este registro CNAME se utiliza para verificar que se posee el dominio que está intentando utilizar. Esto se suma a la creación de un registro D que apunta a la dirección IP virtual de la aplicación web.

Para encontrar la dirección IP, así como el nombre **awverify** y los nombres **.azurewebsites.net** para la aplicación, siga estos pasos:

1. En el explorador, abra el [Portal de Azure](https://portal.azure.com).

2. En la hoja **Aplicaciones web**, haga clic en el nombre de la aplicación web, seleccione **Todas las configuraciones** y seleccione **Dominios personalizados y SSL** en la parte inferior de la página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. En la hoja **Dominios personalizados y SSL**, haga clic en **Traer dominios externos**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

	> [AZURE.NOTE]Con las aplicaciones web en modo **Gratuito** no se pueden usar nombres de dominio personalizados, por lo que el plan del Servicio de aplicaciones se debe actualizar al nivel **Compartido**, **Básico**, **Estándar** o **Premium**. Para obtener más información sobre los planes de tarifa del plan del Servicio de aplicaciones, incluyendo cómo cambiar el plan de tarifa de su aplicación web, consulte [Escalado de aplicaciones web](../articles/web-sites-scale.md).

6. En la hoja **Bring external domains**, verá la información de **awverify**, el nombre de dominio **.azurewebsites.net** asignado actualmente y la dirección IP virtual. Guarde esta información, puesto que se utilizará al crear registros DNS.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

<!---HONumber=Nov15_HO1-->