<properties
 pageTitle="Envío de trabajos a un clúster de HPC Pack en Azure | Microsoft Azure"
 description="Obtenga información sobre cómo configurar un equipo local para enviar trabajos a un clúster de HPC Pack en Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="01/14/2016"
 ms.author="danlep"/>

# Envío de trabajos HPC desde un equipo local a un clúster de HPC Pack implementado en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

En este artículo se muestra cómo configurar un equipo cliente local con Windows para ejecutar herramientas de envío de trabajos de HPC Pack que se comunican con un clúster de HPC Pack en Azure a través de HTTPS. Esto proporciona una forma sencilla y flexible para que los usuarios de varios clústeres envíen trabajos a un clúster de HPC Pack en la nube sin necesidad de conectarse directamente a la VM del nodo principal o acceder a una suscripción de Azure para ejecutar las herramientas de envío de trabajos.

![Enviar un trabajo a un clúster de Azure][jobsubmit]

## Requisitos previos

* Un **nodo principal de HPC Pack implementado en una VM de Azure**: se recomienda usar herramientas automáticas, como una [plantilla de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/) o un [script de Azure PowerShell](virtual-machines-hpcpack-cluster-powershell-script.md), para implementar el nodo principal y el clúster. Para completar los pasos de este artículo se necesitan el nombre DNS del nodo principal y las credenciales de un administrador de clústeres.

* **Soporte de instalación de HPC Pack**: el paquete de instalación gratuito de la versión más reciente de HPC Pack (HPC Pack 2012 R2) está disponible en el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Asegúrese de descargar la misma versión de HPC Pack que está instalada en la VM del nodo principal.

* **Equipo cliente**: necesitará un equipo cliente de Windows o Windows Server que pueda ejecutar las utilidades de cliente de HPC Pack (consulte los [requisitos del sistema](https://technet.microsoft.com/library/dn535781.aspx)). Si solo desea usar el portal web de HPC Pack o la API de REST para enviar trabajos, puede usar el equipo cliente que prefiera.


## Paso 1: Instalar y configurar los componentes web en el nodo principal

Para habilitar una interfaz REST para enviar trabajos al clúster a través de HTTPS, instale y configure los componentes web de HPC Pack en el nodo principal de HPC Pack, en caso de que no estén configurados. Primero instale los componentes web ejecutando el archivo de instalación HpcWebComponents.msi. A continuación, configure los componentes, para lo que debe ejecutar el script de HPC PowerShell **Set-HPCWebComponents.ps1**.

Para obtener información detallada sobre los procedimientos, consulte [Instalación de los componentes web de Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Algunas plantillas de inicio rápido de Azure instalan y configuran los componentes web automáticamente. Si usa el [script de implementación de IaaS de HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) para crear el clúster, puede instalar y configurar opcionalmente los componentes web como parte de la implementación.

**Para instalar los componentes web**

1. Conéctese a la máquina virtual del nodo principal con las credenciales de un administrador de clústeres.

2. En la carpeta de instalación de HPC Pack, ejecute HpcWebComponents.msi en el nodo principal.

3. Siga los pasos del asistente para instalar los componentes web

**Para configurar los componentes web**

1. En el nodo principal, inicie HPC PowerShell como administrador.

2. Para cambiar el directorio a la ubicación del script de configuración, escriba el siguiente comando:

    ```
    cd $env:CCP_HOME\bin
    ```
3. Para configurar la interfaz de REST e iniciar el servicio web HPC, escriba el siguiente comando:

    ```
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Cuando se le pida que seleccione un certificado, elija el certificado que corresponda al nombre DNS público del nodo principal. Por ejemplo, si usa el script de implementación IaaS de HPC Pack para crear el clúster, el nombre del certificado tiene el formato CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net. Si usa una plantilla de inicio rápido de Azure, el nombre del certificado tiene el formato CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.

    >[AZURE.NOTE] Debe seleccionar este certificado para enviar trabajos posteriormente al nodo principal desde un equipo local. No seleccione ni configure un certificado que corresponda al nombre de equipo del nodo principal del dominio de Active Directory (por ejemplo, CN=*MyHPCHeadNode.HpcAzure.local*).

5. Para configurar el portal web para el envío de trabajos, escriba el siguiente comando:

    ```
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Una vez completado el script, detenga y reinicie el servicio del programador de trabajos de HPC escribiendo lo siguiente:

    ```
    net stop hpcscheduler
    net start hpcscheduler
    ```

## Paso 2: Instalar las utilidades de cliente de HPC Pack en un equipo local

Si aún no lo hizo, descargue los archivos de instalación de HPC Pack (instalación completa) del [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024) en el equipo cliente. Al comenzar la instalación, elija la opción de instalación para las utilidades de cliente de HPC Pack.

Para usar las herramientas de cliente de HPC Pack para enviar trabajos a la máquina virtual del nodo principal, también necesitará exportar un certificado desde el nodo principal e instalarlo en el equipo cliente. Necesitará que el certificado esté en formato .CER.

**Para exportar el certificado desde el nodo principal**

1. En el nodo principal, agregue el complemento Certificados a Microsoft Management Console para la cuenta del equipo local. Para que conocer los pasos para agregar el complemento, consulte [Agregar el complemento Certificados a MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. En el árbol de consola, expanda **Certificados – Equipo local**, expanda **Personal** y, a continuación, haga clic en **Certificados**.

3. Busque el certificado que configuró para los componentes web de HPC Pack en el [Paso 1: instalación y configuración de los componentes web en el nodo principal](#step-1:-install-and-configure-the-web-components-on-the-head-node) (por ejemplo, CN=&lt;HeadNodeDnsName&gt;.cloudapp.net).

4. Haga clic con el botón derecho en el certificado, haga clic en **Todas las tareas** y luego en **Exportar**.

5. En el Asistente para exportar certificados, haga clic en **Siguiente** y luego seleccione **No exportar la clave privada**.

6. Siga los restantes pasos del asistente para exportar el certificado en formato DER binario codificado X.509 (.CER).


**Para importar el certificado en el equipo cliente**


1. Copie el certificado que exportó desde el nodo principal a una carpeta del equipo cliente.

2. En el equipo cliente, ejecute certmgr.msc.

3. En el Administrador de certificados, expanda **Certificados – Usuario actual**, expanda **Entidades de certificación raíz de confianza**, haga clic con el botón derecho en **Certificados**, haga clic en **Todas las tareas** y, después, en **Importar**.

4. En el Asistente para importar certificados, haga clic en **Siguiente** y siga los pasos necesarios para importar el certificado que exportó desde el nodo principal al almacén de entidades de certificación raíz de confianza.



>[AZURE.SECURITY] Puede ver una advertencia de seguridad, ya que el equipo cliente no reconocerá la entidad de certificación del nodo principal. Para la realización de pruebas puede omitir esta advertencia y completar la importación del certificado.

## Paso 3: Ejecutar trabajos de prueba en el clúster

Para comprobar la configuración, intente ejecutar trabajos en el clúster de Azure con el equipo local que ejecuta las utilidades cliente de HPC Pack. Por ejemplo, puede usar las herramientas de la GUI o los comandos de la línea de comandos de HPC Pack para enviar trabajos al clúster. También puede usar un portal web para enviar los trabajos.


**Para ejecutar comandos de envío de trabajos en el equipo cliente**


1. En el equipo cliente, inicie un símbolo del sistema.

2. Escriba un comando de ejemplo. Por ejemplo, para enumerar todos los trabajos del clúster, escriba un comando similar a uno de los siguientes, según el nombre DNS completo del nodo principal:

    ```
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all

    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Use el nombre DNS completo del nodo principal, no la dirección IP, en la dirección URL del programador. Si especifica la dirección IP, verá un mensaje de error similar a "El certificado del servidor debe tener una cadena de confianza válida o colocarse en el almacén raíz de confianza".

3. Cuando se le solicite, escriba el nombre de usuario (con el formato &lt;DomainName&gt;\\&lt;UserName&gt;) y la contraseña de administrador de clústeres de HPC u otro usuario de clúster configurado. Puede elegir almacenar las credenciales localmente para realizar más operaciones de trabajo.

    Aparece una lista de trabajos.


**Para usar el Administrador de trabajos de HPC en el equipo cliente**

1. Si anteriormente no almacenó las credenciales de dominio de un usuario de clúster en el equipo cliente cuando envió el trabajo, puede agregarlas en el Administrador de credenciales.

    a. En el Panel de control del equipo cliente, inicie el Administrador de credenciales.

    b. Haga clic en **Credenciales de Windows**, y, a continuación, en **Agregar una credencial genérica**.

    c. Especifique la dirección de Internet, por ejemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler o https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com/HpcScheduler), y escriba el nombre de usuario (con el formato &lt;DomainName&gt;\\&lt;UserName&gt;) y la contraseña del administrador de clústeres HPC o de otro usuario de clúster que haya configurado.

2. En el equipo cliente, inicie el Administrador de trabajos de HPC.

3. En el cuadro de diálogo **Seleccionar nodo principal**, escriba la dirección URL del nodo principal de Azure (por ejemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net o https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com).

    El Administrador de trabajos de HPC se abre y muestra una lista de los trabajos del nodo principal.

**Para usar el portal web en el nodo principal**

1. Inicie un explorador web en el equipo cliente y escriba uno de los siguientes, según el nombre DNS completo del nodo principal:

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal

    https://<HeadNodeDnsName>.<region>cloudapp.azure.com/HpcPortal
    ```
2. En el cuadro de diálogo de seguridad que aparece, escriba las credenciales de dominio del Administrador de clústeres de HPC. (También puede agregar otros usuarios de clúster en distintos roles. Consulte [Administración de usuarios de clúster](https://technet.microsoft.com/library/ff919335.aspx)).

    El portal web se abre en la vista de la lista de trabajos.

3. Para enviar un trabajo de ejemplo que devuelva una cadena “Hello World” desde el clúster, haga clic en **Nuevo trabajo** en el panel de navegación izquierdo.

4. En la página **Nuevo trabajo**, en **Desde páginas de envío**, haga clic en **HelloWorld**. Aparece la página de envío del trabajo.

5. Haga clic en **Enviar**. Si se le solicita, especifique las credenciales de dominio del administrador de clústeres de HPC. El trabajo se envía y el id. del trabajo aparece en la página **Mis trabajos**.

6. Para ver los resultados del trabajo que ha enviado, haga clic en el id. de trabajo y luego haga clic en **Ver tareas** para ver el resultado del comando (en **Salida**).

## Pasos siguientes

* También es posible enviar trabajos al clúster de Azure con la [API de REST de HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Si desea enviar un clúster desde un cliente Linux, consulte el ejemplo de Python en el [SDK de HPC Pack 2012 R2 y el código de ejemplo](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-hpcpack-cluster-submit-jobs/jobsubmit.png

<!---HONumber=AcomDC_0128_2016-->