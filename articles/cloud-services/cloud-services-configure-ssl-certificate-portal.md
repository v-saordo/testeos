<properties 
	pageTitle="Configuración de SSL para un servicio en la nube | Microsoft Azure" 
	description="Aprenda a especificar un punto de conexión HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación. Estos ejemplos usan el Portal de Azure." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# Configuración de SSL para una aplicación en Azure

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-configure-ssl-certificate-portal.md)
- [Azure classic portal](cloud-services-configure-ssl-certificate.md)

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger los datos que se envían por Internet. Esta tarea común analiza cómo especificar un punto de conexión HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación.

> [AZURE.NOTE] Los procedimientos de esta tarea se aplican a Servicios en la nube de Azure; para Servicios de aplicaciones consulte [esto](../app-service-web/web-sites-configure-ssl-certificate.md).

Esta tarea usa una implementación de producción; al final de este tema se proporciona información sobre el uso de una implementación de ensayo.

Lea [esto](cloud-services-how-to-create-deploy-portal.md) primero si aún no ha creado un servicio en la nube.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

## Paso 1: Obtener un certificado SSL

Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio cloudapp.net. Debe adquirir un nombre de dominio personalizado para usarlo cuando obtenga acceso a su servicio. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usó para tener acceso a su aplicación. Por ejemplo, si su nombre de dominio personalizado es **contoso.com** debe solicitar un certificado de su CA para ****.contoso.com** o **www.contoso.com**.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

Para propósitos de prueba, puede [crear](cloud-services-certs-create.md) y usar un certificado autofirmado. Un certificado autofirmado no está autenticado por una CA y puede usar el dominio cloudapp.net como la dirección URL del sitio web. Por ejemplo, la tarea siguiente usa un certificado autofirmado en el que el nombre común (CN) usado en el certificado es **sslexample.cloudapp.net**.

A continuación, debe incluir información sobre el certificado en su definición de servicio y los archivos de configuración del servicio.

<a name="modify"> </a>
## Paso 2: Modificar los archivos de definición y configuración del servicio

Su aplicación debe estar configurada para usar el certificado y se debe agregar un punto de conexión HTTPS. Como resultado, se deben actualizar la definición de servicio y los archivos de configuración del servicio.

1.  En su entorno de desarrollo, abra el archivo de definición de servicio (CSDEF), agregue una sección **Certificates** dentro de la sección **WebRole** e incluya la siguiente información sobre el certificado:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA"
                             permissionLevel="limitedOrElevated" />
            </Certificates>
        ...
        </WebRole>

    La sección **Certificates** define el nombre de nuestro certificado, su ubicación y el nombre de la tienda donde se encuentra.
    
    Se pueden establecer permisos (atributo `permisionLevel`) en uno de los siguientes casos:

    | Valor del permiso | Descripción |
    | ----------------  | ----------- |
    | limitedOrElevated | **(Predeterminado)** todos los procesos de rol pueden tener acceso a la clave privada. |
    | elevated | Solo los procesos elevados pueden tener acceso a la clave privada.|

2.  En el archivo de definición de servicio, agregue un elemento **InputEndpoint** en la sección **Endpoints** para habilitar HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  En el archivo de definición de servicio, agregue un elemento **Binding** en la sección **Sites**. Esto agrega un enlace de HTTPS para asignar el punto de conexión a su sitio:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Se han efectuado todos los cambios necesarios en el archivo de definición de servicio; sin embargo, todavía necesita agregar la información del certificado en el archivo de configuración del servicio.

4.  En el archivo de configuración de servicio (CSCFG), ServiceConfiguration.Cloud.cscfg, agregue una sección **Certificates** en la sección **Role** y reemplace el valor de la huella digital de muestra que se indica a continuación por el de su certificado:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(El ejemplo anterior usa **sha1** para el algoritmo de huella digital. Especifique el valor adecuado para el algoritmo de huella digital de su certificado).

Ahora que se actualizaron los archivos de definición del servicio y configuración del servicio, prepare su implementación para cargarla en Azure. Si va a usar **cspack**, asegúrese de no usar la marca **/generateConfigurationFile**, puesto que así se sobrescribe la información del certificado que acaba de insertar.

## Paso 3: Cargar un certificado

Conéctese al portal y...

1. Seleccione su servicio en la nube de los siguientes modos:
    - En el Portal, seleccione su **Servicio en la nube**. (Que se encontrará en **Examinar todo/área reciente**).
    
        ![Publicación del servicio en la nube](media/cloud-services-configure-ssl-certificate-portal/browse.png)
    
        **O bien**
        
    - En **Examinar todo**, seleccione **Servicios en la nube** en **Filtrar por** y seleccione la instancia de servicio en la nube que desee.

3. Abra la **configuración** para el servicio en la nube.

    ![Abrir configuración](media/cloud-services-configure-ssl-certificate-portal/all-settings.png)

4. Haga clic en **Certificados**.

    ![Haga clic en el icono de certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

4. Proporcione el **Archivo**, la **Contraseña** y luego haga clic en **Cargar**.

## Paso 4: Conectarse a la instancia de rol con HTTPS

Ahora que su implementación está funcionando en Azure, puede conectarse a ella con HTTPS.
    
1.  Haga clic en la **dirección URL del sitio** para abrir el explorador web.

    ![Haga clic en la dirección URL del sitio](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2.  En el explorador web, modifique el vínculo para usar **https** en lugar de **http** y, a continuación, visite la página.

    >[AZURE.NOTE] Si va a usar un certificado autofirmado, cuando vaya a un punto de conexión HTTPS que está asociado al certificado autofirmado, aparecerá un error de certificado en el explorador. El uso de un certificado firmado por una entidad de certificación de confianza elimina el problema; mientras tanto, puede ignorar el error. (Otra opción es agregar el certificado autofirmado a la tienda de certificados de la entidad de certificación de confianza para el usuario).

    ![Vista previa del sitio](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

    >[AZURE.TIP] Si desea usar SSL para una implementación de ensayo en vez de una implementación de producción, tendrá que determinar primero la dirección URL que se usó para la implementación de ensayo. Una vez que se ha implementado el servicio en la nube, la dirección URL del entorno de almacenamiento provisional viene determinada por el GUID del **identificador de implementación** en este formato: `https://deployment-id.cloudapp.net/`
      
    >Cree un certificado con un nombre común (CN) igual a la dirección URL basada en el GUID (por ejemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**), use el Portal para agregar el certificado al servicio en la nube de almacenamiento provisional, agregue la información del certificado a los archivos CSDEF y CSCFG, vuelva a empaquetar la aplicación y actualice la implementación del almacenamiento provisional para usar el paquete y el archivo CSCFG nuevos.

## Pasos siguientes

* [Configuración general de su servicio en la nube](cloud-services-how-to-configure-portal.md).
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* [Administración del servicio en la nube](cloud-services-how-to-manage-portal.md).

<!---HONumber=AcomDC_0128_2016-->