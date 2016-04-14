<properties
	pageTitle="Uso de la red CDN | Microsoft Azure"
	description="Obtenga información acerca del uso de la Red de entrega de contenido (CDN) de Azure para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>


# Integración de una cuenta de almacenamiento con CDN

CDN puede habilitarse para almacenar el contenido de la caché en Almacenamiento de Azure. Ofrece a los desarrolladores una solución global de entrega de contenido con alto ancho de banda; para ello, almacena en memoria caché los blobs y los contenidos estáticos de las instancias de proceso en nodos físicos ubicados en Estados Unidos, Europa, Asia, Australia y Sudamérica.


## Paso 1: Creación de una cuenta de almacenamiento

Use el siguiente procedimiento para crear una nueva cuenta de almacenamiento para una suscripción de Azure. Una cuenta de almacenamiento proporciona acceso a los servicios de almacenamiento de Azure. La cuenta de almacenamiento representa el máximo nivel de espacio de nombres para tener acceso a todos los componentes del servicio de almacenamiento de Azure: servicios BLOB, servicios Cola y servicios Tabla. Para más información, consulte [Introducción al Almacenamiento de Microsoft Azure](../storage/storage-introduction.md).

Para crear una cuenta de almacenamiento, debe ser administrador del servicio o coadministrador de la suscripción correspondiente.

> [AZURE.NOTE] Hay varios métodos que puede usar para crear una cuenta de almacenamiento, incluido el Portal de Azure y Powershell. Para este tutorial, usaremos el Portal de Azure.

**Para crear una cuenta de almacenamiento para una suscripción de Azure**

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.  En la esquina superior izquierda, seleccione **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **Datos + Almacenamiento** y luego haga clic en **Cuenta de almacenamiento**. Deje la opción **Clásica** seleccionada como el modelo de implementación y luego haga clic en **Crear**.

    Aparecerá la hoja de la **Cuenta de almacenamiento**.

    ![Crear una cuenta de almacenamiento][create-new-storage-account]

4. En el campo **Almacenamiento**, escriba un nombre de subdominio. Esta entrada puede contener de 3 a 24 letras minúsculas y números.

    Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción. Para dirigir un recurso contenedor en el servicio BLOB, debería usar un URI en el siguiente formato, en el que *&lt;StorageAccountLabel&gt;* hace referencia al valor que ha escrito en **Escriba una dirección URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante**: La etiqueta de URL forma el subdominio del URI de la cuenta de almacenamiento y debe ser único entre todos los servicios hospedados en Azure.

	Este valor también se utiliza como nombre de esta cuenta de almacenamiento en el portal o en el acceso a esta cuenta mediante programación.

5.  Seleccione un **Plan de tarifa** o use el valor predeterminado. Para más información sobre niveles de precios, vea [Precios de Almacenamiento de Azure](../../pricing/details/storage).

6.  Seleccione o cree un **Grupo de recursos**. Para más información sobre los grupos de recursos, vea [Información general del Administrador de recursos de Azure](resource-group-overview/#resource-groups).

7. Seleccione la **Suscripción** con la que se usará la cuenta de almacenamiento.

8.  Haga clic en **Crear**. El proceso de creación de la cuenta de almacenamiento podría tardar varios minutos en completarse.

9.  Para comprobar que la cuenta de almacenamiento se ha creado correctamente, verifique que la cuenta aparece en la lista de elementos de **Almacenamiento** con el estado **Conectado**.


## Paso 2: Crear un nuevo perfil de CDN

Un perfil de red de entrega de contenido es una colección de puntos de conexión de red de entrega de contenido. Cada perfil contiene uno o más de estos puntos de conexión de CDN. Puede que quiera usar varios perfiles para organizar sus puntos de conexión de la red CDN por dominio de Internet, aplicación web o cualquier otro criterio.

> [AZURE.TIP] Si ya tiene un perfil de red CDN que quiere utilizar para este tutorial, continúe con el [Paso 3](#step-3-create-a-new-cdn-endpoint).

**Para crear un nuevo perfil de CDN**

1. En el [Portal de administración de Azure](https://portal.azure.com), en la parte superior, haga clic en **Nuevo**. En la hoja **Nuevo**, seleccione **Medios + CDN** y, luego, **CDN**.

    Aparece la nueva hoja del perfil de CDN.

    ![Nuevo perfil de CDN][new-cdn-profile]

2. Escriba un nombre para su perfil de CDN.

3. Seleccione un **Plan de tarifa** o use el valor predeterminado.

4. Seleccione o cree un **Grupo de recursos**. No es necesario que este sea el mismo grupo de recursos que la cuenta de almacenamiento.

5. Seleccione la **Suscripción** para este perfil de CDN. Esta deberá ser la misma suscripción que la cuenta de almacenamiento para la finalidad de este tutorial.

6. Seleccione una **Ubicación**. Esta es la ubicación de Azure en la que se almacenará la información de su perfil de red CDN. No tiene ningún impacto en las ubicaciones de puntos de conexión de CDN. No tiene que ser la misma ubicación que la cuenta de almacenamiento.

7. Haga clic en el botón **Crear** para crear el nuevo perfil.

## Paso 3: Crear un nuevo punto de conexión de CDN

**Para crear un nuevo punto de conexión de una red CDN para una cuenta de almacenamiento**

1. En el [Portal de administración de Azure](https://portal.azure.com), vaya a su perfil de CDN. Puede haberlo anclado al panel en el paso anterior. Si no lo hace, para encontrarlo, haga clic en **Examinar**, en **Perfiles de CDN** y luego haga clic en el perfil al que planea agregar el punto de conexión.

    Aparece la hoja del perfil de CDN.

    ![Perfil de CDN][cdn-profile-settings]

2. Haga clic en el botón **Agregar extremo**.

    ![Botón Agregar punto de conexión][cdn-new-endpoint-button]

    Aparecerá la hoja **Agregar un extremo**.

    ![Hoja Agregar punto de conexión][cdn-add-endpoint]

3. Escriba un **Nombre** para este punto de conexión de red de entrega de contenido. Este nombre se usará para obtener acceso a sus recursos almacenados en caché en el dominio `<EndpointName>.azureedge.net`.

4. En la lista desplegable **Tipo de origen**, seleccione *Almacenamiento*.

5. En la lista desplegable **Nombre de host de origen**, seleccione su cuenta de almacenamiento.

6. Deje los valores predeterminados para la **Ruta de acceso de origen**, el **Encabezado de host de origen** y el **Puerto de origen/protocolo**. Debe especificar al menos un protocolo (HTTP o HTTPS).

    > [AZURE.NOTE] Esta configuración habilita todos los contenedores visibles públicamente de la cuenta de almacenamiento para el almacenamiento en caché en la red CDN. Si quiere limitar el ámbito a un contenedor único, use **Ruta de acceso de origen**. Tenga en cuenta que el contenedor debe tener su visibilidad establecida en pública.

7. Haga clic en el botón **Agregar** para crear el nuevo punto de conexión.

8. Una vez creado el punto de conexión, aparecerá en la lista de puntos de conexión del perfil. La visualización de la lista muestra la URL que se debe utilizar para tener acceso al contenido en caché, así como al dominio de origen.

    ![Punto de conexión de CDN][cdn-endpoint-success]

    > [AZURE.NOTE] El punto de conexión no estará disponible inmediatamente para su uso. Se pueden tardar hasta 90 minutos en que el registro se propague a través de la red CDN. Es posible que los usuarios que intenten usar el nombre de dominio de la red CDN de forma inmediata reciban el código de estado 404 hasta que el contenido esté disponible a través de la red CDN.


## Paso 4: Acceso a su contenido de la red CDN

Para obtener acceso al contenido almacenado en la memoria caché de la red CDN, use la URL de la red CDN que se le ha proporcionado en el portal. La dirección del blob en caché será similar a la siguiente:

http://<*NombrePuntoConexión*>.azureedge.net/<*miContenedorPúblico*>/<*NombreDeBlob*>

> [AZURE.NOTE] Una vez que haya habilitado el acceso de la red CDN a una cuenta de almacenamiento o servicio hospedado, todos los objetos disponibles de forma pública se pueden almacenar en la memoria caché perimetral de la red CDN. Si modifica un objeto que está almacenado en la memoria caché de la red CDN actualmente, el nuevo contenido no estará disponible a través de la red CDN hasta que la red CDN actualice su contenido al cumplir el período de vida del contenido almacenado en caché.

## Paso 5: Eliminación de su contenido de la red CDN

Si no desea seguir almacenando un objeto en la memoria caché de la Red de entrega de contenido de Azure (CDN), puede realizar uno de los siguientes pasos:

-   Puede crear un contenedor privado en vez de público. Vea [Administración del acceso de lectura anónimo a contenedores y blobs](../storage-manage-access-to-resources/) para más información.
-   Puede deshabilitar o eliminar el punto de conexión de la red CDN con el Portal de administración.
-   Puede modificar su servicio hospedado para no seguir respondiendo las solicitudes del objeto.

Un objeto que ya está almacenado en la memoria caché de la red CDN permanecerá almacenado en caché hasta que cumpla el período de vida del objeto o hasta que se purgue el punto de conexión. Al cumplir el período de vida, la red CDN comprobará si el punto de conexión de la red CDN sigue siendo válido y si el objeto sigue siendo accesible de forma anónima. Si no lo es, el objeto dejará de estar almacenado en caché.


## Recursos adicionales

-   [Asignación del contenido de la red CDN a un dominio personalizado](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[new-cdn-profile]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_0302_2016-->