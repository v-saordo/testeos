<properties 
	 pageTitle="Habilitar la Red de entrega de contenido (CDN) para Azure" 
	 description="En este tema se muestra cómo habilitar la Red de entrega de contenido (CDN) para Azure." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="01/20/2016" 
	 ms.author="casoper"/>



#Habilitar la Red de entrega de contenido (CDN) para Azure  

CDN puede habilitarse para el origen mediante el Portal de administración de Azure. Se admiten varios tipos de orígenes de Azure integrados, incluidas aplicaciones web, el almacenamiento de blobs y Servicios en la nube. También puede habilitar la red CDN para el punto de conexión de streaming de Servicios multimedia de Azure. Si el origen no es uno de estos servicios de Azure, o se hospeda en otra parte fuera de Azure, también puede crear un origen personalizado. Una vez que haya habilitado un punto de conexión de red CDN para su origen, todos los objetos disponibles de forma pública se pueden almacenar en la memoria caché perimetral de la red CDN.

## Crear un nuevo perfil de CDN

Un perfil de red de entrega de contenido es una colección de puntos de conexión de red de entrega de contenido. Cada perfil contiene uno o más de estos puntos de conexión de CDN. Puede que quiera usar varios perfiles para organizar sus puntos de conexión de la red CDN por dominio de Internet, aplicación web o cualquier otro criterio.

> [AZURE.NOTE]De manera predeterminada, una sola suscripción de Azure está limitada a cuatro perfiles de red CDN. Cada perfil de red CDN está limitado a diez puntos de conexión de red CDN.
>
> Los precios de red de entrega de contenido se aplican en el nivel de perfil de red de entrega de contenido. Si quiere utilizar una combinación de características de red de entrega de contenido estándar y premium, necesitará varios perfiles de red de entrega de contenido.


**Para crear un nuevo perfil de CDN**

1. En el [Portal de administración de Azure](https://portal.azure.com), en la parte superior, haga clic en **Nuevo**. En la hoja **Nuevo**, seleccione **Medios + CDN** y, luego, **CDN**.

    Aparece la nueva hoja del perfil de CDN.
    
    ![Nuevo perfil de CDN][new-cdn-profile]

2. Escriba un nombre para su perfil de CDN.

3. Seleccione un **Plan de tarifa** o use el valor predeterminado.

4. Seleccione o cree un grupo de recursos. Para más información sobre los grupos de recursos, consulte [Información general del Administrador de recursos de Azure](resource-group-overview/#resource-groups).

5. Seleccione la **Suscripción** para este perfil de red de entrega de contenido.

6. Seleccione una **Ubicación**. Esta es la ubicación de Azure en la que se almacenará la información de su perfil de red CDN. No tiene ningún impacto en las ubicaciones de puntos de conexión de CDN. No tiene que ser la misma ubicación que la cuenta de almacenamiento.

7. Haga clic en el botón **Crear** para crear el nuevo perfil.

## Crear un nuevo punto de conexión de CDN

**Para crear un nuevo punto de conexión de una red CDN para una cuenta de almacenamiento**

1. En el [Portal de administración de Azure](https://portal.azure.com), vaya a su perfil de CDN. Puede haberlo anclado al panel en el paso anterior. Si no lo hace, para encontrarlo, haga clic en **Examinar**, en **Perfiles de CDN** y luego haga clic en el perfil al que planea agregar el punto de conexión.

    Aparece la hoja del perfil de CDN.
    
    ![Perfil de CDN][cdn-profile-settings]
    
2. Haga clic en el botón **Agregar punto de conexión**.

    ![Botón Agregar punto de conexión][cdn-new-endpoint-button]

    Aparecerá la hoja **Agregar un punto de conexión**.
    
    ![Hoja Agregar punto de conexión][cdn-add-endpoint]

3. Escriba un **Nombre** para este punto de conexión de red de entrega de contenido. Este nombre se usará para obtener acceso a sus recursos almacenados en caché en el dominio `<EndpointName>.azureedge.net`.

4. En la lista desplegable **Tipo de origen**, seleccione su tipo de origen.
	
	![Tipo de origen de la red CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. En la lista desplegable **Nombre de host de origen**, seleccione o escriba su dominio de origen. En la lista desplegable se muestran todos los orígenes disponibles del tipo especificado en el paso 4. Si seleccionó *Origen personalizado* como su **Tipo de origen**, tendrá que escribir el dominio de su origen personalizado.

6. En el cuadro de texto **Ruta de acceso de origen**, escriba la ruta de acceso a los recursos que quiera almacenar en caché o déjela en blanco para permitir almacenar en caché cualquier recurso en el dominio especificado en el paso 5.

7. En el **Encabezado del host de origen**, escriba el encabezado de host que quiera que la red CDN envíe con cada solicitud o deje el valor predeterminado.

8. Para **Protocolo** y **Puerto de origen**, especifique los protocolos y los puertos que se usan para tener acceso a sus recursos en el origen. Sus clientes seguirán usando estos mismos protocolos y puertos cuando tienen acceso a recursos de la red CDN. Se debe seleccionar al menos un protocolo (HTTP o HTTPS).

9. Haga clic en el botón **Agregar** para crear el nuevo punto de conexión.

10. Una vez creado el punto de conexión, aparecerá en la lista de puntos de conexión del perfil. La visualización de la lista muestra la URL que se debe utilizar para tener acceso al contenido en caché, así como al dominio de origen.

    ![Punto de conexión de CDN][cdn-endpoint-success]

    > [AZURE.NOTE]El punto de conexión no estará disponible inmediatamente para su uso. Se pueden tardar hasta 90 minutos en que el registro se propague a través de la red CDN. Es posible que los usuarios que intenten usar el nombre de dominio de la red CDN de forma inmediata reciban el código de estado 404 hasta que el contenido esté disponible a través de la red CDN.

##Otras referencias
- [Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado](cdn-map-content-to-custom-domain.md)
- [Depuración de un punto de conexión de red de entrega de contenido de Azure](cdn-purge-endpoint.md)

[new-cdn-profile]: ./media/cdn-create-new-endpoint/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
 

<!---HONumber=AcomDC_0121_2016-->