<properties
   pageTitle="Implementar un clúster Deis de 3 nodos | Microsoft Azure"
   description="En este artículo se describe cómo crear un clúster Deis de 3 nodos en Azure mediante una plantilla de Administrador de recursos de Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="HaishiBai"
   manager="larar"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# Implementar un clúster Deis de 3 nodos

Este artículo le guiará a través de aprovisionamiento de un clúster [Deis](http://deis.io/) en Azure. Abarca todos los pasos de creación de los certificados necesarios para implementar y escalar una aplicación **Go** de ejemplo en el clúster recién suministrado.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


El siguiente diagrama muestra la arquitectura del sistema implementado. Un administrador del sistema administra el clúster con herramientas Deis como **deis** y **deisctl**. Las conexiones se establecen a través de un equilibrador de carga de Azure, que reenvía las conexiones a uno de los nodos de los miembros del clúster. Los clientes acceden a aplicaciones implementadas a través del equilibrador de carga también. En este caso, el equilibrador de carga reenvía el tráfico a una malla de enrutador Deis, que enruta el tráfico a los contenedores Docker correspondientes alojados en el clúster.

  ![Diagrama de arquitectura del clúster Desis implementado](media/virtual-machines-deis-cluster/architecture-overview.png)

Para ejecutar los siguientes pasos, necesitará:

 * Una suscripción de Azure activa. Si no tiene una, puede obtener una prueba gratuita en [azure.com](https://azure.microsoft.com/).
 * Un identificador profesional o educativo para usar grupos de recursos de Azure. Si tiene una cuenta personal e inicia sesión con un identificador de Microsoft, deberá [crear un identificador profesional a partir del suyo personal](resource-group-create-work-id-from-personal.md).
 * O bien, dependiendo de su sistema operativo de cliente: el [Azure PowerShell](powershell-install-configure.md) o el [CLI de Azure para Mac, Linux y Windows](xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL se usa para generar los certificados necesarios.
 * Un cliente Git como [Git Bash](https://git-scm.com/).
 * Para probar la aplicación de ejemplo, también necesitará un servidor DNS. Puede usar los servidores o servicios DNS que admiten los registros de carácter comodín A.
 * Un equipo para ejecutar herramientas de cliente Deis. Puede usar un equipo local o en una máquina virtual. Puede ejecutar estas herramientas en casi cualquier distribución de Linux, pero las siguientes instrucciones uszan Ubuntu.

## Aprovisionamiento del clúster

En esta sección, usará una plantilla de [Administrador de recursos de Azure](../resource-group-overview.md) del repositorio de código abierto [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). En primer lugar, copiará la plantilla. A continuación, creará un nuevo par de claves SSH para la autenticación. Y, a continuación, configurará un nuevo identificador para su clúster. Por último, usará el script Shell o PowerShell para aprovisionar el clúster.

1. Clone el repositorio: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Vaya a la carpeta de la plantilla:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Cree un nuevo par de claves SSH mediante ssh-keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Genere un certificado con la clave privada anterior:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Vaya a [https://discovery.etcd.io/new](https://discovery.etcd.io/new) para generar un nuevo token de clúster, que presenta un aspecto similar al siguiente:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br /> Cada clúster CoreOS debe tener un token único de este servicio gratuito. Consulte [Documentación de CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) para obtener más detalles.

6. Modifique el archivo **cloud-config.yaml** para reemplazar el token de **detección** existente por el nuevo token:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modifique el archivo **azuredeploy parameters.json**: abra el certificado creado en el paso 4 en un editor de texto. Copie todo el texto entre `----BEGIN CERTIFICATE-----` y `-----END CERTIFICATE-----` en el parámetro **sshKeyData** (deberá quitar todos los caracteres de nueva línea).

8. Modifique el parámetro **newStorageAccountName**. Esta es la cuenta de almacenamiento para los discos del sistema operativo de la máquina virtual. Este nombre de cuenta debe ser único globalmente.

9. Modifique el parámetro **publicDomainName**. Esto pasará a formar parte del nombre DNS asociado con la IP pública del equilibrador de carga. El FQDN final tendrá el formato de _[valor de este parámetro]_._[región]_.cloudapp.azure.com. Por ejemplo, si especifica el nombre como deishbai32 y el grupo de recursos se implementa en la región Oeste de EE. UU., el FQDN final del equilibrador de carga final será deishbai32.westus.cloudapp.azure.com.

10. Guarde el archivo de parámetros. A continuación, podrá aprovisionar el clúster usando Azure PowerShell:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  o la CLI de Azure:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Una vez que se ha aprovisionado el grupo de recursos, puede ver todos los recursos del grupo en el Portal de Azure clásico. Como se muestra en la siguiente captura de pantalla, el grupo de recursos contiene una red virtual con tres máquinas virtuales que se han unido al mismo conjunto de disponibilidad. El grupo también contiene un equilibrador de carga, que tiene una dirección IP pública asociada.

  ![Grupo de recursos aprovisionado en el Portal de Azure clásico](media/virtual-machines-deis-cluster/resource-group.png)

## Instalar el cliente

Necesita **deisctl** para controlar su clúster Deis. Aunque deisctl se instala automáticamente en todos los nodos del clúster, es recomendable usar deisctl en un equipo administrativo independiente. Además, dado que todos los nodos se configuran únicamente con direcciones IP privadas, necesitará usar tunelización SSH a través del equilibrador de carga, que tiene una dirección IP pública, para conectarse a las máquinas de nodo. Estos son los pasos de configuración de deisctl en una máquina virtual o física Ubuntu independiente.

1. Instalar deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Agregue su clave privada al agente ssh:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configure deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

La plantilla define reglas NAT de entrada que asignan 2223 a la instancia 1, 2224 a la instancia 2 y 2225 a la instancia 3. Esto proporciona redundancia para el uso de la herramienta deisctl. Puede examinar estas reglas en el Portal de Azure clásico:

![Reglas NAT en el equilibrador de carga](media/virtual-machines-deis-cluster/nat-rules.png)

> [AZURE.NOTE] La plantilla solo admite actualmente clústeres de 3 nodos. Esto es debido a una limitación en la definición de la regla NAT de la plantilla del Administrador de recursos de Azure, que no admite la sintaxis del bucle.

## Instalar e iniciar la plataforma Deis

Ahora puede usar deisctl para instalar e iniciar la plataforma Deis:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Iniciar la plataforma tarda unos instantes (hasta 10 minutos). Especialmente, iniciar el servicio generador puede tardar mucho tiempo. En ocasiones precisa unos intentos para iniciar correctamente: si parece que la operación se bloquea, intente escribir `ctrl+c` para interrumpir la ejecución del comando y vuelva a intentarlo.

Puede usar `deisctl list` para comprobar si se están ejecutando todos los servicios:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

¡Enhorabuena! Ahora ya dispone de un clúster Deis en ejecución en Azure. A continuación, implementemos un ejemplo de aplicación Go para ver el clúster en acción.

## Implementar y escalar una aplicación Hello World

Los pasos siguientes muestran cómo implementar una aplicación Go "Hello World" en el clúster. Los pasos se basan en la [documentación Deis](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Para que la malla de enrutamiento funcione correctamente, deberá tener un registro de carácter comodín A para el dominio que apunta a la dirección IP pública del equilibrador de carga. La captura de pantalla siguiente muestra el registro A de un registro de dominio de ejemplo en GoDaddy:

    ![Registro de Godaddy A](media/virtual-machines-deis-cluster/go-daddy.png)
<p />
2. Instalar deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Cree una nueva clave SSH y, a continuación, agregue la clave pública a GitHub (por supuesto, también se pueden reutilizar las claves existentes). Cree un nuevo par de claves SSH, use:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Agregue id\_rsa.pub o la clave pública que desee a GitHub. Puede hacerlo mediante el botón Agregar clave SSH en la pantalla de configuración de claves SSH:

  ![Clave de Github](media/virtual-machines-deis-cluster/github-key.png) <p /> 5. Registre un nuevo usuario:

        deis register http://deis.[your domain]
<p />
6. Agregue la clave SSH:

        deis keys:add [path to your SSH public key]
  <p />
7. Cree una aplicación.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8. La inserción de git desencadenará imágenes Docker para generar e implementar, lo cual llevará unos minutos. Según mi experiencia, en ocasiones es posible que el paso 10 (insertar la imagen en el repositorio privado) se cuelgue. Cuando esto suceda, puede detener el proceso, quitar la aplicación mediante `deis apps:destroy –a <application name>` e intentarlo de nuevo. Puede usar `deis apps:list` para averiguar el nombre de la aplicación. Si todo funciona, debería ver algo parecido a lo siguiente al final de las salidas de comando:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Compruebe si la aplicación funciona:

        curl -S http://[your application name].[your domain]
  Debería ver lo siguiente:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Escalar la aplicación a 3 instancias:

        deis scale cmd=3
<p />
11. Si lo desea, puede usar información de para examinar los detalles de su aplicación. Los resultados siguientes son de la implementación de mi aplicación:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## Pasos siguientes

En este artículo le guiamos a través de todos los pasos para aprovisionar un nuevo clúster Deis en Azure mediante una plantilla de Administrador de recursos de Azure. La plantilla admite la redundancia en conexiones de herramientas, así como el equilibrio de cargas para aplicaciones implementadas. La plantilla también evita el uso de direcciones IP públicas en los nodos de miembro, lo cual ahorra valiosos recursos IP públicos y proporciona un entorno más seguro para hospedar aplicaciones. Para obtener más información, consulte los artículos siguientes:

[Información general sobre el Administrador de recursos de Azure][resource-group-overview] [Cómo usar la CLI de Azure][azure-command-line-tools] [Uso de Azure PowerShell con el Administrador de recursos de Azure][powershell-azure-resource-manager]

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0128_2016-->