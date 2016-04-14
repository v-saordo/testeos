<properties 
    pageTitle="Configuraciones de seguridad de escala elástica | Microsoft Azure" 
    description="Configuración de certificados x409 para cifrado" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/23/2016" 
    ms.author="torsteng;sidneyh" />


# Configuración de seguridad de división y combinación  

Para usar el servicio de división y combinación, debe configurar correctamente la seguridad. El servicio forma parte de la característica de Escalado elástico de la Base de datos SQL de Microsoft Azure. Para obtener más información, vea el [Tutorial del servicio de división y combinación de Escalado elástico](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## Configuración de certificados

Los certificados se configuran de dos maneras.

1. [Configuración del certificado SSL](#To-Configure-the-SSL#Certificate)
2. [Configuración del certificado de cliente](#To-Configure-Client-Certificates) 

## Obtención de certificados

Los certificados se pueden obtener de Entidades de certificación (CA) públicas o desde el [Servicio de certificados de Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Estos son los métodos preferidos para obtener certificados.

Si esas opciones no están disponibles, puede generar **certificados autofirmados**.
 
## Herramientas para generar certificados

* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### Ejecución de las herramientas

* Desde el Símbolo del sistema para desarrolladores de Visual Studio, consulte [Símbolo del sistema de Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Si está instalado, vaya a:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obtenga el WDK de [Windows 8.1: descargar kits y herramientas](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## Configuración del certificado SSL
Se requiere un certificado SSL para cifrar la comunicación y autenticar el servidor. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

### Creación de un nuevo certificado autofirmado

1.    [Creación de un certificado autofirmado](#Create-a-Self-Signed-Certificate)
2.    [Creación del archivo PFX para el certificado SSL autofirmado](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Carga del certificado SSL en el servicio en la nube](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Actualización del certificado SSL en el archivo de configuración de servicio](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Importación de la Entidad de certificación SSL](#Import-SSL-Certification-Authority)

### Para utilizar un certificado existente desde el almacén de certificados
1. [Exportación del certificado SSL desde el almacén de certificados](#Export-SSL-Certificate-From-Certificate-Store)
2. [Carga del certificado SSL en el servicio en la nube](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Actualización del certificado SSL en el archivo de configuración de servicio](#Update-SSL-Certificate-in-Service-Configuration-File)

### Para utilizar un certificado existente en un archivo PFX

1. [Carga del certificado SSL en el servicio en la nube](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Actualización del certificado SSL en el archivo de configuración de servicio](#Update-SSL-Certificate-in-Service-Configuration-File)

## Configuración del certificado de cliente
Se requieren certificados de cliente para autenticar solicitudes al servicio. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

### Desactivación de los certificados de cliente
1.    [Desactivación de la autenticación basada en certificado de cliente](#Turn-Off-Client-Certificate-Based-Authentication)

### Emisión de nuevos certificados de cliente autofirmados
1.    [Creación de una Entidad de certificación autofirmada](#Create-a-Self-Signed-Certification-Authority)
2.    [Carga de un certificado de Entidad de certificación en el servicio en la nube](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Actualización de un certificado de Entidad de certificación en un archivo de configuración de servicio](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Emisión de certificados de cliente](#Issue-Client-Certificates)
5.    [Creación de archivos PFX para certificados de cliente](#Create-PFX-files-for-Client-Certificates)
6.    [Importación de certificados de cliente](#Import-Client-Certificate)
7.    [Copia de las huellas digitales de certificados de cliente](#Copy-Client-Certificate-Thumbprints)
8.    [Configuración de clientes autorizados en el archivo de configuración de servicio](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### Uso de certificados de cliente existentes
1.    [Búsqueda de clave pública de Entidad de certificación](#Find-CA-Public Key)
2.    [Carga de un certificado de Entidad de certificación en el servicio en la nube](#Upload-CA-certificate-to-cloud-service)
3.    [Actualización de un certificado de Entidad de certificación en un archivo de configuración de servicio](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Copia de las huellas digitales de certificados de cliente](#Copy-Client-Certificate-Thumbprints)
5.    [Configuración de clientes autorizados en el archivo de configuración de servicio](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Configuración de la comprobación de revocación de certificado de cliente](#Configure-Client-Certificate-Revocation-Check)

## Direcciones IP permitidas

El acceso a los extremos de servicio puede estar limitado a intervalos concretos de direcciones IP.

## Configuración del cifrado para el almacén

Se requiere un certificado para cifrar las credenciales que se almacenan en el almacén de metadatos. Elija el escenario más aplicable entre los tres que aparecen a continuación y ejecute todos sus pasos:

### Use un nuevo certificado autofirmado

1.     [Creación de un certificado autofirmado](#Create-a-Self-Signed-Certificate)
2.     [Crear archivo PFX para certificado de cifrado autofirmado](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Cargar el certificado de cifrado en el servicio en la nube](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Actualización del certificado de cifrado en el archivo de configuración de servicio](#Update-Encryption-Certificate-in-Service-Configuration-File)

### Utilizar un certificado existente desde el almacén de certificados

1.     [Exportar el certificado de cifrado del almacén de certificados](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Cargar el certificado de cifrado en el servicio en la nube](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Actualización del certificado de cifrado en el archivo de configuración de servicio](#Update-Encryption-Certificate-in-Service-Configuration-File)

### Utilizar un certificado existente de un archivo PFX

1.     [Cargar el certificado de cifrado en el servicio en la nube](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Actualización del certificado de cifrado en el archivo de configuración de servicio](#Update-Encryption-Certificate-in-Service-Configuration-File)

## La configuración predeterminada

La configuración predeterminada deniega todo acceso al extremo HTTP. Esta es la configuración recomendada, dado que las solicitudes a estos extremos pueden llevar información delicada, como pueden ser las credenciales de base de datos. La configuración predeterminada permite todo acceso al extremo HTTPS. Esta configuración puede ser aún más restrictiva.

### Cambio de la configuración

El grupo de reglas de control de acceso que se aplican a un extremo se configuran en la sección **<EndpointAcls>** del **archivo de configuración de servicio**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Las reglas de un grupo de control de acceso se configuran en una sección <AccessControl name=""> del archivo de configuración de servicio.

El formato se explica en la documentación de listas de control de acceso de red. Por ejemplo, para permitir únicamente a las direcciones IP del intervalo comprendido entre 100.100.0.0 y 100.100.255.255 el acceso al extremo HTTPS, las reglas se parecerían a esta:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## Prevención de la denegación de servicio

Existen dos mecanismos distintos compatibles para detectar y evitar los ataques de denegación de servicio:

*    Restringir la cantidad de solicitudes simultáneas por host remoto (desactivado de manera predeterminada)
*    Restringir la velocidad de acceso por host remoto (activado de manera predeterminada)

Estos mecanismos se basan en las características más documentadas en Seguridad de IP dinámica en IIS. Cuando cambie esta configuración, tenga cuidado con los siguientes factores:

* El comportamiento de los servidores proxy y los dispositivos de traducción de direcciones de red sobre la información de host remoto
* Se considera cada solicitud a cualquier recurso en el rol web (por ejemplo, carga de scripts, imágenes, etc.)

## Restricción de la cantidad de acceso simultáneos

Los ajustes que configuran este comportamiento son:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Cambie DynamicIpRestrictionDenyByConcurrentRequests a true para habilitar esta protección.

## Restricción de la velocidad del acceso

Los ajustes que configuran este comportamiento son:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## Configuración de la respuesta a una solicitud denegada

Los siguientes ajustes configuran la respuesta a una solicitud denegada:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Consulte la documentación para Seguridad de IP dinámica en IIS para otros valores compatibles.

## Operaciones para configurar certificados de servicio
Este tema es solo para referencia. Siga los pasos de configuración descritos en:

* Configuración del certificado SSL
* Configuración de certificados de cliente

## Creación de un certificado autofirmado
Ejecute:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Personalizar:

*    -n con la dirección URL de servicio. Caracteres comodín ("CN=*.cloudapp.net") y nombres alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") son compatibles.
*    -e con la fecha de caducidad del certificado Cree una contraseña segura y especifíquela cuando se le solicite.

## Creación del archivo PFX para el certificado SSL autofirmado

Ejecute:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Escriba la contraseña y luego exporte el certificado con estas opciones: * Sí, exportar la clave privada * Exportar todas las propiedades extendidas

## Exportación del certificado SSL desde el almacén de certificados

* Busque el certificado
* Haga clic en Acciones -> Todas las tareas -> Exportar…
* Exporte el certificado a un archivo .PFX con estas opciones:
    * Sí, exportar la clave privada
    * Incluir todos los certificados en la ruta de certificación si es posible *Exportar todas las propiedades extendidas

## Carga del certificado SSL en el servicio en la nube

Cargue el certificado con el archivo .PFX existente o generado con el par de claves SSL:

* Escriba la contraseña para proteger la información de clave privada

## Actualización del certificado SSL en el archivo de configuración de servicio

Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## Importación de la Entidad de certificación SSL

Siga estos pasos en todas las cuentas/máquinas que se comunicarán con el servicio:

* Haga doble clic en el archivo .CER en el Explorador de Windows
* En el cuadro de diálogo Certificado, haga clic en Instalar certificado...
* Importe el certificado al almacén de Entidades de certificación de raíz de confianza

## Desactivación de la autenticación basada en certificado de cliente

Solo se admite la autenticación basada en certificado de cliente y deshabilitarla permitirá el acceso público a los extremos del servicio, a menos que existan otros mecanismos (por ejemplo, Red virtual de Microsoft Azure).

Cambie esta configuración a falso en el archivo de configuración del servicio para deshabilitar la característica:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

A continuación, copie la misma huella digital del certificado SSL en la configuración del certificado de CA:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## Creación de una Entidad de certificación autofirmada
Ejecute los siguientes pasos para crear un certificado autofirmado que actúe como Entidad de certificación:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Para personalizarlo

*    -e con la fecha de caducidad de la certificación


## Búsqueda de clave pública de Entidad de certificación

Una Entidad de certificación de la confianza del servicio debe haber emitido todos los certificados de cliente. Encuentre la clave pública para la Entidad de certificación que emitió los certificados de cliente que se van a usar para autenticación a fin de cargarla al servicio en la nube.

Si el archivo con la clave pública no está disponible, expórtelo desde el almacén de certificados:

* Busque el certificado
    * Busque un certificado de cliente emitido por la misma Entidad de certificación
* Haga doble clic en el certificado.
* Seleccione la pestaña Ruta de certificación en el cuadro de diálogo Certificado.
* Haga doble clic en la entrada de CA de la ruta.
* Tome notas de las propiedades del certificado.
* Cierre el cuadro de diálogo **Certificado**.
* Busque el certificado
    * Busque la CA anotada anteriormente.
* Haga clic en Acciones -> Todas las tareas -> Exportar…
* Exporte el certificado a un archivo .CER con estas opciones:
    * **No, no exportar la clave privada**
    * Incluir todos los certificados en la ruta de certificación si es posible.
    * Exportar todas las propiedades extendidas.

## Carga de un certificado de Entidad de certificación en el servicio en la nube

Cargue el certificado con el archivo .CER existente o generado con la clave pública de CA.

## Actualización de un certificado de Entidad de certificación en un archivo de configuración de servicio

Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Actualice el valor de la siguiente configuración con la misma huella digital:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## Emisión de certificados de cliente

Cada persona autorizada a tener acceso al servicio debe tener un certificado de cliente emitido para su uso exclusivo y debe elegir su propia contraseña segura para proteger su clave privada.

Los siguientes pasos se deben ejecutar en la misma máquina donde se generó y almacenó el certificado de CA autofirmado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalización:

* -n con un identificador para el cliente que se autenticará con este certificado
* -e con la fecha de caducidad del certificado
* MyID.pvk y MyID.cer con nombres de archivo únicos para este certificado de cliente

Este comando solicitará que se cree una contraseña y se use una vez. Utilice una contraseña segura.

## Creación de archivos PFX para certificados de cliente

Para cada certificado de cliente generado, ejecute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalización:

    MyID.pvk and MyID.cer with the filename for the client certificate

Escriba la contraseña y, a continuación, exporte el certificado con estas opciones:

* Sí, exportar la clave privada
* Exportar todas las propiedades extendidas
* La persona para quien se emite este certificado debe elegir la contraseña de exportación

## Importación de certificados de cliente

Cada persona para quien se ha emitido un certificado de cliente debe importar el par de claves en las máquinas que usará para comunicarse con el servicio:

* Haga doble clic en el archivo .PFX en el Explorador de Windows
* Importe el certificado al almacén personal con al menos esta opción:
    * Incluir todas las propiedades extendidas comprobadas

## Copia de las huellas digitales de certificados de cliente
Cada usuario para el que se ha emitido un certificado de cliente debe seguir estos pasos para obtener la huella digital de su certificado, el que se agregará al archivo de configuración de servicio: * Ejecute certmgr.exe * Seleccione la pestaña Personal * Haga doble clic en el certificado de cliente que se usará para la autenticación * En el cuadro de diálogo Certificado que se abre, seleccione la pestaña Detalles * Asegúrese de que Mostrar esté mostrando Todo * Seleccione el campo denominado Huella digital en la lista * Copie el valor de la huella digital ** Elimine los caracteres Unicode no visibles delante del primer dígito ** Elimine todos los espacios

## Configuración de clientes autorizados en el archivo de configuración de servicio

Actualice el valor de los siguientes ajustes en el archivo de configuración de servicio con una lista delimitada por comas de las huellas digitales de los certificados de cliente que tienen permitido el acceso al servicio:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## Configuración de la comprobación de revocación de certificado de cliente

La configuración predeterminada no se comprueba con la Entidad de certificación para el estado de revocación de certificado de cliente. Para activar las comprobaciones, si la Entidad de certificación que emitió los certificados de cliente admite dichas comprobaciones, cambie la siguiente configuración por uno de los valores definidos en la enumeración X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## Crear archivo PFX para certificados de cifrado autofirmados

Para un certificado de cifrado, ejecute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalización:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Escriba la contraseña y luego exporte el certificado con estas opciones: * Sí, exportar la clave privada * Exportar todas las propiedades extendidas * Necesitará la contraseña al cargar el certificado en el servicio en la nube.

## Exportar el certificado de cifrado del almacén de certificados

*    Busque el certificado
*    Haga clic en Acciones -> Todas las tareas -> Exportar…
*    Exporte el certificado a un archivo .PFX con estas opciones: 
  *    Sí, exportar la clave privada
  *    Incluir todos los certificados en la ruta de certificación si es posible 
*    Exportar todas las propiedades extendidas

## Cargar el certificado de cifrado en el servicio en la nube

Cargue el certificado con el archivo .PFX existente o generado con el par de claves de cifrado:

* Escriba la contraseña para proteger la información de clave privada

## Actualización del certificado de cifrado en el archivo de configuración de servicio

Actualice el valor de huella digital de la siguiente configuración en el archivo de configuración de servicio con la huella digital del certificado cargado al servicio en la nube:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## Operaciones comunes de certificado

* Configuración del certificado SSL
* Configuración de certificados de cliente

## Busque el certificado

Siga estos pasos:

1. Ejecute mmc.exe.
2. Archivo -> Agregar o quitar complemento…
3. Seleccione **Certificados**.
4. Haga clic en **Agregar**.
5. Elija la ubicación del almacén de certificados.
6. Haga clic en **Finalizar**
7. Haga clic en **Aceptar**.
8. Expanda **Certificados**.
9. Expanda el nodo del almacén de certificados.
10. Expanda el nodo secundario Certificado.
11. Seleccione un certificado en la lista.

## Exportación de certificado
En el **asistente para exportar certificados**:

1. Haga clic en **Siguiente**.
2. Seleccione **Sí** y **Exportar la clave privada**.
3. Haga clic en **Siguiente**.
4. Seleccione el formato deseado del archivo de salida.
5. Marque las opciones deseadas.
6. Marque **Contraseña**.
7. Escriba una contraseña segura y confírmela.
8. Haga clic en **Siguiente**.
9. Escriba o busque un nombre de archivo donde almacenar el certificado (use una extensión .PFX).
10. Haga clic en **Siguiente**.
11. Haga clic en **Finalizar**
12. Haga clic en **Aceptar**.

## Importación de certificado

En el asistente para importar certificados:

1. Seleccione la ubicación del almacén.

    * Seleccione **Usuario actual**, si solo los procesos en ejecución bajo el usuario actual tendrán acceso al servicio
    * Seleccione **Máquina local**, si otros procesos de este equipo tendrán acceso al servicio
2. Haga clic en **Siguiente**.
3. Si se importa desde un archivo, confirme la ruta del archivo.
4. Si se importa un archivo .PFX:
    1.     Escriba la contraseña para proteger la clave privada
    2.     Seleccione las opciones de importación
5.     Seleccione "Colocar" certificados en el siguiente almacén
6.     Haga clic en **Examinar**.
7.     Seleccione el almacén deseado.
8.     Haga clic en **Finalizar**
       
    * Si se eligió el almacén de Entidad de certificación raíz de confianza, haga clic en **Sí**.
9.     Haga clic en **Aceptar** en todas las ventanas de diálogo.

## Carga del certificado

En el [Portal de Azure](https://portal.azure.com/)

1. Seleccione **Servicios en la nube**.
2. Seleccione el servicio en la nube.
3. Haga clic en **Certificados** en el menú superior.
4. En la barra inferior, haga clic en **Cargar**.
5. Seleccione el archivo de certificado.
6. Si es un archivo .PFX, escriba la contraseña de la clave privada.
7. Una vez realizado, copie la huella digital del certificado desde la entrada nueva en la lista.

## Otras consideraciones de seguridad
 
La configuración SSL descrita en este documento cifra la comunicación entre el servicio y sus clientes cuando se usa el extremo HTTPS. Esto es importante, porque las credenciales para el acceso a la base de datos y potencialmente a otra información confidencial están contenidas en la comunicación. Sin embargo, tenga en cuenta que el servicio persiste en el estado interno, incluidas credenciales, en sus tablas internas en la base de datos SQL de Microsoft Azure que ha proporcionado para el almacenamiento de metadatos en la suscripción de Microsoft Azure. Esa base de datos se definió como parte de los siguientes ajustes en el archivo de configuración de servicio (archivo .CSCFG):

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Las credenciales almacenadas en esta base de datos están cifradas. Sin embargo, como práctica recomendada, asegúrese de que los roles de web y de trabajo de sus implementaciones de servicio se mantienen actualizados y seguros, a medida que ambos tienen acceso a la base de datos de metadatos y el certificado usado para el cifrado y descifrado de credenciales almacenadas.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!---HONumber=AcomDC_0224_2016-->