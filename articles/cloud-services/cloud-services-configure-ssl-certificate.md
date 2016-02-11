<properties 
	pageTitle="Configuración de SSL en un servicio en la nube (clásico) | Microsoft Azure" 
	description="Aprenda a especificar un punto de conexión HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación." 
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

Lea [esto](cloud-services-how-to-create-deploy.md) primero si aún no ha creado un servicio en la nube.

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

## Paso 2: modificar la definición del servicio y los archivos de configuración

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

Su paquete de implementación se actualizó para usar el certificado y se agregó un punto de conexión HTTPS. Ahora podrá cargar el paquete y el certificado en Azure con el Portal de Azure clásico.

1. Inicie sesión en el [Portal de Azure clásico][]. 
2. Haga clic en **Servicios en la nube** en el panel de navegación izquierdo.
3. Haga clic en el servicio en la nube deseado.
4. Haga clic en la ficha **Certificados**.

    ![Haga clic en la pestaña Certificados](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Haga clic en el botón **Upload**.

    ![Cargar](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Proporcione el **archivo**, la **contraseña** y, a continuación, haga clic en **Completar** (la marca de verificación).

## Paso 4: Conectarse a la instancia de rol con HTTPS

Ahora que su implementación está funcionando en Azure, puede conectarse a ella con HTTPS.

1.  En el Portal de Azure clásico, seleccione su implementación y, a continuación, haga clic en el vínculo de **Dirección URL del sitio**.

    ![Determinar URL del sitio][2]

2.  En el explorador web, modifique el vínculo para usar **https** en lugar de **http** y, a continuación, visite la página.

    **Nota:** si va a usar un certificado autofirmado, cuando vaya a un extremo HTTPS que está asociado al certificado autofirmado, aparecerá un error de certificado en el explorador. El uso de un certificado firmado por una entidad de certificación de confianza elimina el problema; mientras tanto, puede ignorar el error. (Otra opción es agregar el certificado autofirmado a la tienda de certificados de la entidad de certificación de confianza para el usuario).

    ![Ejemplo de sitio web con SSL][3]

Si desea usar SSL para una implementación de ensayo en vez de una implementación de producción, tendrá que determinar primero la dirección URL que se usó para la implementación de ensayo. Implemente su servicio en la nube para el entorno de ensayo sin incluir un certificado ni ninguna información del certificado. Una vez implementado, puede determinar la dirección URL basada en el GUID, que se incluye en el campo **Dirección URL del sitio** del Portal de Azure clásico. Cree un certificado con un nombre común (CN) igual a la dirección URL basada en el GUID (por ejemplo, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**), use el Portal de Azure clásico para agregar el certificado al servicio en la nube de ensayo, agregue la información del certificado a los archivos CSDEF y CSCFG, vuelva a empaquetar la aplicación y actualice la implementación de ensayo para usar el paquete y el archivo CSCFG nuevos.

## Pasos siguientes

* [Configuración general de su servicio en la nube](cloud-services-how-to-configure.md).
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* [Administración de su servicio en la nube](cloud-services-how-to-manage.md).


  [Portal de Azure clásico]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png

<!---HONumber=AcomDC_0128_2016-->