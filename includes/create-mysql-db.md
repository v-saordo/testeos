Esta guía le mostrará cómo usar [ClearDB] para la creación de una base de datos MySQL desde la [Tienda de Azure] y cómo crear una base de datos MySQL como un recurso vinculado cuando cree un [Sitio web Azure][waws]. [ClearDB] es un proveedor de base de datos como servicio tolerante a errores que le permite ejecutar y administrar bases de datos MySQL en centros de datos de Azure y conectarse a ellos desde cualquier aplicación.

> [AZURE.NOTE]Cuando cree una base de datos MySQL como parte del proceso de creación del sitio web, solo se le permitirá crear una base de datos gratuita. Si crea una base de datos MySQL desde la Tienda de Azure, podrá crear una base de datos gratuita o seleccionar opciones de pago.

## Creación de una base de datos MySQL desde la Tienda de Azure

Para crear una base de datos MySQL desde la [Tienda de Azure], haga lo siguiente:

1. Inicie sesión en el [Portal de administración de Azure][portal].
2. Haga clic en **+NUEVO** en la parte inferior de la página y, a continuación, seleccione **MARKETPLACE**.

	![Seleccionar complemento de la tienda](./media/create-mysql-db/select-store.png)

3. Seleccione **Base de datos MySQL de ClearDB** y, a continuación, haga clic en la flecha que aparece en la parte inferior del marco.

	![Seleccionar la base de datos ClearDB MySQL](./media/create-mysql-db/select-cleardb-mysql.png)

4. Seleccione un plan, especifique un nombre de base de datos, seleccione una región y, a continuación, haga clic en la flecha de la parte inferior del marco.

	![Comprar la base de datos MySQL de la tienda](./media/create-mysql-db/purchase-mysql.png)

5. Haga clic en la marca de verificación para completar su compra.

	![Revisar y finalizar la compra](./media/create-mysql-db/complete-mysql-purchase.png)

6. Una vez que se ha creado la base de datos, puede administrarla desde la pestaña **COMPLEMENTOS** del Portal de administración.

	![Administración de la base de datos MySQL en el portal de Azure](./media/create-mysql-db/manage-mysql-add-on.png)

7. Puede obtener acceso a la información de la conexión de la base de datos mediante un clic en **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la página (mostrada anteriormente).

	![Información de la conexión MySql](./media/create-mysql-db/mysql-conn-info.png)


## Creación de una base de datos MySQL como recurso vinculado para el sitio web de Azure

Para crear una base de datos MySQL como un recurso vinculado cuando crea un [Sitio web Azure][waws], haga lo siguiente:

1. Inicie sesión en el [Portal de administración de Azure][portal].
2. Haga clic en **+NUEVO** en la página y, a continuación, seleccione **PROCESO**, **SITIO WEB** y **CREAR CON BASE DE DATOS**.

	![Crear sitio web con base de datos](./media/create-mysql-db/custom_create.png)

3. Proporcione una **DIRECCIÓN URL** para el sitio web, seleccione la **REGIÓN** del sitio y elija **Crear una nueva base de datos MySQL** en el menú desplegable **BASE DE DATOS**. Opcionalmente, puede sustituir el nombre determinado para la cadena de conexión. Haga clic en la flecha de la parte inferior de la página.

	![Información del sitio web](./media/create-mysql-db/provide-website-details.png)

4. Proporcione el **NOMBRE** de una base de datos, seleccione la **REGIÓN** de la base de datos (debe ser la misma que la región del sitio web), acepte las condiciones legales de ClearDB y haga clic en la marca de verificación de la parte inferior del marco.

	![Información de MySQL](./media/create-mysql-db/provide-mysql-details.png)

5. Una vez que se haya creado el sitio web, haga clic en el nombre del sitio para ir al panel del sitio.

	![Ir al panel del sitio web](./media/create-mysql-db/go-to-website-dashboard.png)

6. Haga clic en **CONFIGURAR**.

	![Ir a la pestaña de configuración](./media/create-mysql-db/go-to-configure-tab.png)

7. Desplácese a la sección **Cadenas de conexión** y haga clic en **Mostrar cadenas de conexión**.

	![Mostrar la cadena de conexión](./media/create-mysql-db/show-conn-string.png)

8. Copie la cadena de conexión para usarla en su aplicación.

	![Mostrar cadena de conexión](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE]La aplicación del sitio web puede obtener acceso a las cadenas de conexión mediante el nombre de cadena de conexión. En las aplicaciones .NET, las cadenas de conexión están disponibles en el objeto **connectionStrings**. En otros lenguajes de programación, se puede obtener acceso a las cadenas de conexión como variables de entorno. Para obtener más información, consulte [Configuración de sitios web][configure].

[ClearDB]: http://www.cleardb.com/
[waws]: /documentation/services/web-sites/
[Tienda de Azure]: ../articles/store.md
[portal]: http://manage.windowsazure.com
[configure]: ../article/app-service-web/web-sites-configure.md

<!---HONumber=Oct15_HO3-->