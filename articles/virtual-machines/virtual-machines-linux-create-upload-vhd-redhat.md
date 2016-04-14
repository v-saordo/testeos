<properties
	pageTitle="Creación y carga de un VHD de Red Hat Enterprise Linux para su uso en Azure"
	description="Aprenda a crear y cargar un disco duro virtual (VHD) de Azure que contiene un sistema operativo Red Hat Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/23/2015"
	ms.author="mingzhan"/>


# Preparación de una máquina virtual basada en Red Hat para Azure
En este artículo, aprenderá a preparar una máquina virtual de Red Hat Enterprise Linux (RHEL) para usarla en Azure. Las versiones de RHEL tratadas en este artículo son 6.7, 7.1 y 7.2, y los hipervisores para la preparación citados en este artículo son Hyper-V, KVM y VMware. Para más información sobre los requisitos para poder participar en el programa de acceso a la nube de Red Hat, vea el [sitio web de acceso a la nube de Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) y [Ejecución de RHEL en Azure](https://access.redhat.com/articles/1989673).

[Preparar una máquina virtual RHEL 6.7 desde el Administrador de Hyper-V](#rhel67hyperv)

[Preparar una máquina virtual RHEL 7.1/7.2 desde el Administrador de Hyper-V](#rhel7xhyperv)

[Preparar una máquina virtual RHEL 6.7 desde KVM](#rhel67kvm)

[Preparar una máquina virtual RHEL 7.1/7.2 desde KVM](#rhel7xkvm)

[Preparar una máquina virtual RHEL 6.7 desde VMware](#rhel67vmware)

[Preparar una máquina virtual RHEL 7.1/7.2 desde VMware](#rhel7xvmware)

[Preparar una máquina virtual RHEL 7.1/7.2 desde un archivo kickstart](#rhel7xkickstart)


## Preparación de una imagen desde el Administrador de Hyper-V
### Requisitos previos
En esta sección, se supone que ya instaló en un disco duro virtual (VHD) una imagen RHEL desde un archivo ISO que obtuvo del sitio web de Red Hat. Para más información acerca de cómo usar el Administrador de Hyper-V para instalar una imagen de sistema operativo, vea [Instalación del rol Hyper-V y configuración de una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalación de RHEL**

- el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet convert-vhd powershell.

- Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. LVM o RAID se pueden utilizar en discos de datos si así se prefiere.

- No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.

- El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.

- Al utilizar img qemu para convertir las imágenes de disco al formato VHD, tenga en cuenta que hay un problema conocido en las versiones de img qemu > = 2.2.1 que da como resultado un VHD con formato incorrecto. El problema se corregirá en una próxima versión de qemu-img. Por ahora es recomendable usar qemu-img versión 2.2.0 o inferior.

### <a id="rhel67hyperv"> </a>Preparar una máquina virtual RHEL 6.7 desde el Administrador de Hyper-V###


1.	En el Administrador de Hyper-V, seleccione la máquina virtual.

2.	Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3.	Desinstale NetworkManager ejecutando el comando siguiente:

        # sudo rpm -e --nodeps NetworkManager

    **Nota:** si el paquete todavía no está instalado, se producirá un mensaje de error en este comando. Se espera que esto sea así.

4.	Cree un archivo llamado **network** en el directorio `/etc/sysconfig/` que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Cree un archivo llamado **ifcfg-eth0** en el directorio `/etc/sysconfig/network-scripts/` que contenga el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

8.	Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	El paquete WALinuxAgent `WALinuxAgent-<version>` se insertó en el repositorio Fedora EPEL 6. Habilite el repositorio epel ejecutando el comando siguiente:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra `/boot/grub/menu.lst` en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto deshabilitará NUMA debido a un error en la versión del kernel usada por RHEL 6.

    Además de lo anterior, se recomienda quitar los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.

    Es posible dejar la opción crashkernel configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de máquina virtual más reducidos.

11.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado. Modifique /etc/ssh/sshd\_config para que incluya la siguiente línea:

        ClientAliveInterval 180

12.	Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **Nota**: La instalación del paquete WALinuxAgent eliminará los paquetes NetworkManager y NetworkManager-gnome, si es que aún no se han eliminado como se indica en el paso 2.

13.	No cree un espacio de intercambio en el disco del sistema operativo. El agente Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se expone en la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # sudo subscription-manager unregister

15.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

### <a id="rhel7xhyperv"> </a>Preparar una máquina virtual RHEL 7.1/7.2 desde el Administrador de Hyper-V###

1.  En el Administrador de Hyper-V, seleccione la máquina virtual.

2.	Haga clic en Conectar para abrir la ventana de consola de la máquina virtual.

3.	Cree un archivo llamado **network** en el directorio `/etc/sysconfig/` que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.	Cree un archivo llamado **ifcfg-eth0** en el directorio `/etc/sysconfig/network-scripts/` que contenga el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.	Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

6.	Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra `/etc/default/grub` en un editor de texto y edite el parámetro **GRUB\_CMDLINE\_LINUX**; por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Además de lo anterior, se recomienda quitar los parámetros siguientes:

        rhgb quiet crashkernel=auto
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Es posible dejar la opción crashkernel configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de máquina virtual más reducidos.

8.	Una vez que termine de editar `/etc/default/grub` tal como se indicó antes, ejecute el comando siguiente para volver a compilar la configuración de GRUB:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

        ClientAliveInterval 180

10.	El paquete WALinuxAgent `WALinuxAgent-<version>` se insertó en el repositorio Fedora EPEL 6. Habilite el repositorio epel ejecutando el comando siguiente:

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11.	Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12.	No cree espacio de intercambio en el disco del SO. El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (vea el paso anterior), modifique los parámetros siguientes en `/etc/waagent.conf` de la forma adecuada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Si quiere anular el registro de la suscripción, ejecute el siguiente comando:

        # sudo subscription-manager unregister

14.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.


## Preparación de una imagen desde KVM

### <a id="rhel67kvm"> </a>Preparar una máquina virtual RHEL 6.7 desde KVM###


1.	Descargue la imagen KVM de RHEL 6.7 desde el sitio web de Red Hat.

2.	Establezca una contraseña raíz.

    Genere la contraseña cifrada y copie el resultado del comando:

        # openssl passwd -1 changeme
    Establezca una contraseña raíz con guestfish:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit
    Cambie el segundo campo del usuario raíz de "!!" a la contraseña cifrada.

3.	Cree una máquina virtual en KVM a partir de la imagen qcow2, establezca el tipo de disco en **qcow2** y establezca el modelo de dispositivo de interfaz de Red virtual en **virtio**. Después, inicie la máquina virtual e inicie sesión como raíz.

4.	Cree un archivo llamado **network** en el directorio `/etc/sysconfig/` que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Cree un archivo llamado **ifcfg-eth0** en el directorio `/etc/sysconfig/network-scripts/` que contenga el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

        # chkconfig network on

8.	Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra `/boot/grub/menu.lst` en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto deshabilitará NUMA debido a un error en la versión del kernel usada por RHEL 6.

    Además de lo anterior, se recomienda quitar los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Es posible dejar la opción crashkernel configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de máquina virtual más reducidos.

10.	Desinstale cloud-init:

        # yum remove cloud-init

11.	Asegúrese de que el servidor SSH esté instalado y configurado para iniciarse en el tiempo de arranque:

        # chkconfig sshd on

    Modifique /etc/ssh/sshd\_config para que incluya las siguientes líneas:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

		# service sshd restart

12.	El paquete WALinuxAgent `WALinuxAgent-<version>` se insertó en el repositorio Fedora EPEL 6. Habilite el repositorio epel ejecutando el comando siguiente:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13.	Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # yum install WALinuxAgent
        # chkconfig waagent on

14.	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en **/etc/waagent.conf**:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # subscription-manager unregister

16.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Apague la máquina virtual en KVM.

18.	Convierta la imagen qcow2 al formato vhd. Primero convierta la imagen al formato raw:

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Asegúrese de que el tamaño de imagen sin procesar se alinea con 1MB. De lo contrario, redondee el tamaño para alinear con 1MB: # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB)).

         # qemu-img resize rhel-6.7.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual de tamaño fijo:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Preparar una máquina virtual RHEL 7.1/7.2 desde KVM###


1.	Descargue la imagen de KVM de RHEL 7.1 (o 7.2) en el sitio web de Red Hat; se usará RHEL 7.1 como ejemplo.

2.	Establezca una contraseña raíz.

    Genere la contraseña cifrada y copie el resultado del comando.

        # openssl passwd -1 changeme

    Establezca una contraseña raíz con guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Cambie el segundo campo del usuario raíz de "!!" a la contraseña cifrada.

3.	Cree una máquina virtual en KVM a partir de la imagen qcow2, establezca el tipo de disco en **qcow2** y establezca el modelo de dispositivo de interfaz de Red virtual en **virtio**. Después, inicie la máquina virtual e inicie sesión como raíz.

4.	Cree un archivo llamado **network** en el directorio `/etc/sysconfig/` que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	Cree un archivo llamado **ifcfg-eth0** en el directorio `/etc/sysconfig/network-scripts/` que contenga el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

        # chkconfig network on

7.	Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra `/etc/default/grub` en un editor de texto y edite el parámetro **GRUB\_CMDLINE\_LINUX**; por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Además de lo anterior, se recomienda quitar los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Es posible dejar la opción crashkernel configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de máquina virtual más reducidos.

9.	Una vez que termine de editar `/etc/default/grub` tal como se indicó antes, ejecute el comando siguiente para volver a compilar la configuración de GRUB:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Agregue módulos de Hyper-V a initramfs:

    Edite `/etc/dracut.conf` y agregue contenido:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Vuelva a generar initramfs:

        # dracut –f -v

11.	Desinstale cloud-init:

        # yum remove cloud-init

12.	Asegúrese de que el servidor SSH esté instalado y configurado para iniciarse en el tiempo de arranque:

        # systemctl enable sshd

    Modifique /etc/ssh/sshd\_config para que incluya las siguientes líneas:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Reinicie sshd:

        systemctl restart sshd

13.	El paquete WALinuxAgent `WALinuxAgent-<version>` se insertó en el repositorio Fedora EPEL 6. Habilite el repositorio epel ejecutando el comando siguiente:

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

14.	Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # yum install WALinuxAgent

    Habilite el servicio waagent:

        # systemctl enable waagent.service

15.	No cree espacio de intercambio en el disco del SO. El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique los parámetros siguientes en `/etc/waagent.conf` de la forma adecuada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # subscription-manager unregister

17.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18.	Apague la máquina virtual en KVM.

19.	Convierta la imagen qcow2 al formato vhd.

    Primero convierta la imagen al formato raw:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Asegúrese de que el tamaño de la imagen sin procesar se ajuste a 1 MB; en caso contrario, redondee al alza el tamaño para adaptarlo a 1 MB:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual de tamaño fijo:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## Preparación de una imagen desde VMware
### Requisitos previos
En esta sección se supone que ya instaló una máquina virtual RHEL en VMware. Para obtener más información acerca de cómo instalar un sistema operativo en VMware, consulte la [Guía de instalación de sistema operativo invitado de VMware](http://partnerweb.vmware.com/GOSIG/home.html).

- Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. LVM o RAID se pueden utilizar en discos de datos si así se prefiere.

- No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.

- Al crear el disco duro virtual, seleccione **Almacenar disco virtual como un solo archivo**.



### <a id="rhel67vmware"> </a>Preparar una máquina virtual RHEL 6.7 desde VMware###

1.	Desinstale NetworkManager ejecutando el comando siguiente:

         # sudo rpm -e --nodeps NetworkManager

    **Nota:** si el paquete todavía no está instalado, se producirá un mensaje de error en este comando. Se espera que esto sea así.

2.	Cree un archivo llamado **network** en el directorio /etc/sysconfig/ que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	Cree un archivo llamado **ifcfg-eth0** en el directorio /etc/sysconfig/network-scripts/ que contenga el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

6.	Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	El paquete WALinuxAgent `WALinuxAgent-<version>` se insertó en el repositorio Fedora EPEL 6. Habilite el repositorio epel ejecutando el comando siguiente:

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/boot/grub/menu.lst" en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto deshabilitará NUMA debido a un error en la versión del kernel usada por RHEL 6. Además de lo anterior, se recomienda quitar los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Es posible dejar la opción crashkernel configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de máquina virtual más reducidos.

9.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

        ClientAliveInterval 180

10.	Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11.	No cree un espacio de intercambio en el disco del sistema operativo:

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (vea el paso anterior), modifique los parámetros siguientes en `/etc/waagent.conf` de la forma adecuada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # sudo subscription-manager unregister

13.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	Apague la máquina virtual y convierta el archivo VMDK en un archivo VHD.

    Primero convierta la imagen al formato raw:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Asegúrese de que el tamaño de la imagen sin procesar se ajuste a 1 MB; en caso contrario, redondee al alza el tamaño para adaptarlo a 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Preparar una máquina virtual RHEL 7.1/7.2 desde VMware###

1.	Cree un archivo llamado **network** en el directorio /etc/sysconfig/ que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	Cree un archivo denominado **ifcfg-eth0** en el directorio /etc/sysconfig/network-scripts/ que contenga el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

4.	Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra `/etc/default/grub` en un editor de texto y edite el parámetro **GRUB\_CMDLINE\_LINUX**; por ejemplo:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Además de lo anterior, se recomienda quitar los parámetros siguientes:

        rhgb quiet crashkernel=auto

    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Es posible dejar la opción crashkernel configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de máquina virtual más reducidos.

6.	Una vez que termine de editar `/etc/default/grub` tal como se indicó antes, ejecute el comando siguiente para volver a compilar la configuración de GRUB:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Agregue módulos de Hyper-V a initramfs:

    Edite `/etc/dracut.conf` y agregue contenido:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Vuelva a generar initramfs:

        # dracut –f -v

8.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

        ClientAliveInterval 180

9.	El paquete WALinuxAgent `WALinuxAgent-<version>` se insertó en el repositorio Fedora EPEL 6. Habilite el repositorio epel ejecutando el comando siguiente:


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10.	Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	No cree espacio de intercambio en el disco del SO. El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (vea el paso anterior), modifique los parámetros siguientes en `/etc/waagent.conf` de la forma adecuada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Si quiere anular el registro de la suscripción, ejecute el siguiente comando:

        # sudo subscription-manager unregister

13.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	Apague la máquina virtual y convierta el archivo VMDK al formato VHD.

    Primero convierta la imagen al formato raw:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Asegúrese de que el tamaño de la imagen sin procesar se ajuste a 1 MB; en caso contrario, redondee al alza el tamaño para adaptarlo a 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## Preparación de una imagen ISO con un archivo Kickstart automáticamente


### <a id="rhel7xkickstart"> </a>Preparar una máquina virtual RHEL 7.1/7.2 desde un archivo kickstart###


1.	Cree el archivo Kickstart con el siguiente contenido y guárdelo. Para más información sobre la instalación Kickstart, consulte la [guía de instalación Kickstart](https://access.redhat.com/documentation/es-ES/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).


        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end



2.	Coloque el archivo Kickstart en un lugar accesible desde el sistema de la instalación.

3.	En el Administrador de Hyper-V, cree una nueva máquina virtual. En la página **Conectar disco duro virtual**, seleccione **Exponer un disco duro virtual más adelante** y complete el Asistente para crear una máquina virtual.

4.	Abra la configuración de máquina virtual:

    a. Adjunte un nuevo disco duro virtual en la máquina virtual y asegúrese de seleccionar **Formato del disco duro virtual** y **Tamaño fijo**.

    b. Exponga la imagen ISO de instalación en la unidad de DVD.

    c. Configure el BIOS para que arranque desde el CD.

5.	Inicie la máquina virtual y, cuando aparezca la guía de instalación, presione el **tabulador** para configurar las opciones de arranque.

6.	Escriba `inst.ks=<the location of the Kickstart file>` al final de las opciones de arranque y presione **Entrar**.

7.	Espere a que la instalación finalice, momento en que la máquina virtual se apagará automáticamente. El VHD de Linux ya está listo para cargarse en Azure.

## Problemas conocidos
Existen problemas conocidos al usar RHEL 7.1 en Hyper-V y Azure.

### Problema: Inmovilización de E/S de disco

Este problema puede producirse durante las actividades frecuentes de E/S de disco de almacenamiento con RHEL 7.1 en Hyper-V y Azure.

Frecuencia de reproducción:

Este problema es intermitente. No obstante, se produce más a menudo durante las operaciones frecuentes de E/S de disco en Hyper-V y Azure.


[AZURE.NOTE] Red Hat ya ha solucionado este problema conocido. Para instalar las correcciones asociadas, ejecute el comando siguiente:

    # sudo yum update


## Pasos siguientes
Ya está listo para usar el disco duro virtual de Red Hat Enterprise Linux para crear nuevas máquinas virtuales de Azure. Si esta es la primera vez que está cargando el archivo .vhd en Azure, consulte los pasos 2 y 3 de [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-create-upload-vhd.md).

Para obtener más información sobre los hipervisores certificados para ejecutar Red Hat Enterprise Linux, visite el [sitio web de Red Hat](https://access.redhat.com/certified-hypervisors).

<!---HONumber=AcomDC_0211_2016-->