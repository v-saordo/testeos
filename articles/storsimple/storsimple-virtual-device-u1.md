<properties 
   pageTitle="Actualización 1 del dispositivo virtual de StorSimple | Microsoft Azure"
   description="Obtenga información acerca de cómo crear, implementar y administrar un dispositivo virtual StorSimple en una red virtual de Microsoft Azure. (Se aplica a la actualización 1 de StorSimple)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/01/2015"
   ms.author="alkohli" />

# Implementar y administrar un dispositivo virtual StorSimple en Azure

[AZURE.INCLUDE [storsimple-version-selector-sva](../../includes/storsimple-version-selector-sva.md)]

##Información general
El dispositivo virtual de StorSimple es una capacidad adicional que se incluye con la solución de Microsoft Azure StorSimple. El dispositivo virtual de StorSimple se ejecuta en una máquina virtual en una red virtual de Microsoft Azure y se puede usar para realizar copias de seguridad y clonar los datos de los hosts. Los temas siguientes le ayudan a conocer, configurar y usar el dispositivo virtual de StorSimple.

- ¿Cómo difiere el dispositivo virtual del dispositivo físico

- Consideraciones de seguridad para utilizar un dispositivo virtual

- Requisitos previos para el dispositivo virtual

- Creación y configuración del dispositivo virtual

- Trabajo con el dispositivo virtual

- Conmutación por error en el dispositivo virtual

- Cerrar o eliminar el dispositivo virtual

Este tutorial se aplica a todos los dispositivos virtuales StorSimple que ejecutan Update 1.

## ¿Cómo difiere el dispositivo virtual del dispositivo físico

El dispositivo virtual de StorSimple es una versión solo de software de StorSimple que se ejecuta en un único nodo en una máquina virtual de Microsoft Azure. El dispositivo virtual admite escenarios de recuperación ante desastres en el que el dispositivo físico no está disponible y es adecuado para su uso en la recuperación de niveles de elementos de copias de seguridad, en la recuperación ante desastres local y en escenarios de prueba y desarrollo de la nube.

### Diferencias del dispositivo físico

Las siguientes son algunas diferencias clave entre el dispositivo virtual de StorSimple y el dispositivo físico de StorSimple:

- El dispositivo virtual solo tiene una interfaz de red: DATA 0. El dispositivo físico tiene seis interfaces de red: de DATA 0 a DATA 5.
- El dispositivo virtual se registra durante la fase de configuración en lugar de como una tarea independiente.
- No se puede volver a generar la clave de cifrado de datos de servicio desde el dispositivo virtual. Durante la sustitución de clave, volverá a generar la clave en el dispositivo físico y, a continuación, actualiza el dispositivo virtual con la clave nueva.
- Si las actualizaciones deben aplicarse al dispositivo virtual, sufrirá un cierto periodo de inactividad, mientras que el dispositivo físico no lo sufrirá.

## Consideraciones de seguridad para utilizar un dispositivo virtual

Tenga en cuenta las siguientes consideraciones de seguridad al utilizar el dispositivo virtual de StorSimple:

- El dispositivo virtual se protege mediante su suscripción de Microsoft Azure. Esto significa que si está utilizando el dispositivo virtual y se pone en peligro su suscripción de Azure, los datos almacenados en el dispositivo virtual también se ponen en peligro.

- La clave pública del certificado usada para cifrar los datos almacenados en Azure StorSimple está disponible de forma segura en el Portal de Azure clásico; la clave privada se conserva con el dispositivo StorSimple. En el dispositivo virtual de StorSimple, las claves públicas y privadas se almacenan en Azure.

- El dispositivo virtual está hospedado en el centro de datos de Microsoft Azure.


## Requisitos previos para el dispositivo virtual

Las siguientes secciones le ayudará a prepararse para usar el dispositivo virtual de StorSimple.

### Requisitos de Azure

Antes de aprovisionar el dispositivo virtual, deberá realizar los siguientes preparativos en el entorno de Azure:

- Para el dispositivo virtual, [configure una red virtual en Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). 
- Puede usar el servidor DNS predeterminado proporcionado por Azure en lugar de especificar su propio nombre del servidor DNS. Si el nombre del servidor DNS no es válido, se producirá un error en la creación del dispositivo virtual.
- Punto a sitio y sitio a sitio son opcionales, pero no obligatorios. Si lo desea, puede configurar estas opciones para escenarios más avanzados. 

>[AZURE.IMPORTANT]**Asegúrese de que la red virtual está en la misma región que las cuentas de almacenamiento en la nube que va a usar con el dispositivo virtual.**

- Puede crear [Máquinas virtuales de Azure](../virtual-machines/virtual-machines-about.md) (servidores host) en la red virtual que pueden usar los volúmenes expuestos por el dispositivo virtual. Estos servidores deben cumplir los siguientes requisitos: 							
	- Estar en máquinas virtuales de Windows o Linux con el software iSCSI Initiator instalado
	- Ejecutarse en la misma red virtual como el dispositivo virtual
	- Ser capaz de conectarse al destino iSCSI del dispositivo virtual a través de la dirección IP interna del dispositivo virtual

- Asegúrese de que haya habilitado la compatibilidad para el tráfico iSCSI y en la nube en la misma red virtual.

### Requisitos de StorSimple

Realice las siguientes actualizaciones en su servicio StorSimple de Azure antes de crear un dispositivo virtual:


- Agregue [registros de control de acceso](storsimple-manage-acrs.md) para las máquinas virtuales que vayan a ser servidores de host para el dispositivo virtual.

- Asegúrese de que tiene un [cuenta de almacenamiento](storsimple-manage-storage-accounts.md#add-a-storage-account) en la misma región que el dispositivo virtual. Las cuentas de almacenamiento en regiones diferentes pueden causar un bajo rendimiento.

- Asegúrese de utilizar una cuenta de almacenamiento para la creación de un dispositivo virtual diferente de la que se usa para los datos. Utilizar la misma cuenta de almacenamiento puede causar un bajo rendimiento.

Asegúrese de que tiene la siguiente información antes de empezar:


- Tiene su cuenta del Portal de Azure clásico con credenciales de acceso.

- Tiene las credenciales de acceso a su cuenta de almacenamiento de Azure.

- Tiene una copia de la clave de cifrado de datos del servicio desde el dispositivo físico.

- Tiene una copia de la clave de cifrado del almacenamiento en la nube para cada contenedor de volumen.


## Creación y configuración del dispositivo virtual

Antes de realizar estos procedimientos, asegúrese de que se han cumplido los [requisitos previos para el dispositivo virtual](#prerequisites-for-the-virtual-device).

Después de completar estos procedimientos, está listo para [trabajar con el dispositivo virtual](#work-with-the-StorSimple-virtual-device).

### Creación de un dispositivo virtual

Después de haber creado una red virtual, configurado un servicio de StorSimple Manager y registrado el dispositivo físico de StorSimple con el servicio, puede utilizar los siguientes pasos para crear un dispositivo virtual de StorSimple.

Realice los pasos siguientes para crear el dispositivo virtual de StorSimple.

1.  En el Portal de Azure clásico, vaya al servicio **StorSimple Manager**.

2. Vaya a la página **Dispositivos**. Haga clic en **Crear un dispositivo virtual** en la parte inferior de la página **Dispositivos**.

3. En el **cuadro de diálogo Crear un dispositivo virtual**, especifique los detalles siguientes.

     ![Crear dispositivo virtual de StorSimple](./media/storsimple-virtual-device-u1/StorSimple_CreateVirtualDevice1.png)

	1. **Nombre**: un nombre único para el dispositivo virtual.

	2. **Versión**: elija la versión del dispositivo virtual. Esta opción estará ausente si solo dispone de dispositivos físicos Update 1 (o posteriores) registrados con este servicio. Este campo se muestra solo si tiene una combinación de dispositivos físicos anteriores y posteriores a Update 1 registrados con el servicio. Dado que la versión del dispositivo virtual determinará desde qué dispositivo físico puede efectuar la conmutación por error o la clonación, es importante que cree una versión adecuada del dispositivo virtual. Seleccione:

	   - Actualización de versión 0.3 si conmuta por error o efectúa una clonación desde un dispositivo físico con el lanzamiento de GA o las actualizaciones de 0.1 a 0.3. 
	   - Actualización de versión 1 si conmuta por error o efectúa una clonación desde un dispositivo físico con la actualización 1 (o versiones posteriores). Elegir Update 1 en la lista desplegable proporcionará realmente un dispositivo virtual Update 1.1.
 
	3. **Red virtual**: el nombre de la red virtual que desea usar con este dispositivo virtual.

	4. **Subred**: la subred de la red virtual que se usa con el dispositivo virtual.

	5. **Cuenta de almacenamiento para la creación de un dispositivo virtual**: esta cuenta de almacenamiento se usará para almacenar la imagen del dispositivo virtual durante el aprovisionamiento y hospedará los discos del dispositivo virtual después del aprovisionamiento. Esta cuenta de almacenamiento debe estar en la misma región que el dispositivo virtual y la red virtual. No debe utilizarse para el almacenamiento de datos por el dispositivo físico o el dispositivo virtual. De forma predeterminada, para ello se creará una nueva cuenta de almacenamiento. Sin embargo, si sabe que ya tiene una cuenta de almacenamiento que es adecuada para este uso, puede seleccionarla en la lista.

    >[AZURE.NOTE]El dispositivo virtual solo puede funcionar con las cuentas de almacenamiento de Azure. Otros proveedores de servicios en la nube como Amazon, HP y OpenStack (que son compatibles con el dispositivo físico) no se admiten para el dispositivo virtual StorSimple.
	
4. Haga clic en la marca de verificación para indicar que sabe que los datos almacenados en el dispositivo virtual estarán hospedados en un centro de datos de Microsoft. Cuando se utiliza solo un dispositivo físico, la clave de cifrado se mantiene con el dispositivo; por lo tanto, Microsoft no podrá descifrarla. ![Fase de creación de dispositivo virtual de StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceCreating1M.png)

    Cuando se utiliza un dispositivo virtual, la clave de cifrado y la clave de descifrado se almacenan en Microsoft Azure. Para obtener más información, consulte [Consideraciones de seguridad para utilizar un dispositivo virtual](#security-considerations-for-using-a-virtual-device).

### Configuración y registro del dispositivo virtual

Antes de comenzar este procedimiento, asegúrese de que tiene una copia de la clave de cifrado de datos del servicio. La clave de cifrado de datos del servicio se creó cuando se configuró el primer dispositivo de StorSimple y se le pidió que lo guardara en una ubicación segura. Si no tiene una copia de la clave de cifrado de datos del servicio, debe ponerse en contacto con Microsoft Support para obtener ayuda.

Realice los pasos siguientes para configurar y registrar el dispositivo virtual de StorSimple.

1. Seleccione el **dispositivo virtual StorSimple** que acaba de crear en la página Dispositivos. 

2. Haga clic en **Completar la instalación del dispositivo**. Esto inicia al Asistente para configurar dispositivos.

    ![Completar configuración de dispositivo de StorSimple en la página Dispositivos](./media/storsimple-virtual-device-u1/StorSimple_CompleteDeviceSetupSVA1M.png)

3. Escriba la **Clave de cifrado de datos de servicio** en el espacio proporcionado.

4. Escriba las contraseñas del administrador de dispositivos y de instantáneas de la longitud y la configuración especificadas.

5. Haga clic en la marca de verificación para finalizar la configuración inicial y el registro del dispositivo virtual.

    ![Configuración de dispositivo virtual de StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceSettings1.png)

Una vez que complete la configuración y el registro, el dispositivo se conectará. (Esto puede demorar varios minutos).

![Fase en línea de dispositivo virtual de StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceOnline1M.png)


### Modificación de la configuración del dispositivo

En la siguiente sección se describen las opciones de configuración de dispositivo que puede configurar para el dispositivo virtual StorSimple si desea usar CHAP, Administrador de instantáneas StorSimple o cambiar la contraseña del Administrador de dispositivos.

#### Configuración del iniciador de CHAP (opcional)

Este parámetro contiene las credenciales que espera el dispositivo virtual (destino) de los iniciadores (servidores) que intentan obtener acceso a los volúmenes. Los iniciadores proporcionarán un nombre de usuario y una contraseña CHAP para identificarse en el dispositivo durante la autenticación.

#### Configuración del destino de CHAP (opcional)

Este parámetro contiene las credenciales que el dispositivo virtual utiliza cuando un iniciador habilitado para CHAP solicita la autenticación mutua o bidireccional. El dispositivo virtual utilizará un nombre de usuario de CHAP inverso y una contraseña de CHAP inversa para identificarse con el iniciador durante este proceso de autenticación. Tenga en cuenta que los valores de destino de CHAP son valores globales. Cuando se aplican, todos los volúmenes conectados al dispositivo virtual de almacenamiento utilizan la autenticación CHAP. Seleccione el dispositivo en la página Dispositivos. Vaya a la página Configurar de su página de dispositivos y desplácese hacia abajo hasta encontrar la sección CHAP.

#### Configuración de StorSimple Snapshot Manager (opcional)

El software StorSimple Snapshot Manager reside en el host de Windows y permite a los administradores administrar copias de seguridad del dispositivo StorSimple en forma de instantáneas locales y en la nube.

>[AZURE.NOTE]Para el dispositivo virtual, el host de Windows es una VM de Azure.

Al configurar un dispositivo en StorSimple Snapshot Manager, se le pedirá que proporcione la dirección IP del dispositivo StorSimple y la contraseña para autenticar el dispositivo de almacenamiento.

Realice los pasos siguientes para cambiar la contraseña de StorSimple Snapshot Manager.

1. En el dispositivo virtual, vaya a **Dispositivos > Configurar**.

2. Desplácese hacia abajo a la sección **Administrador de instantáneas**. Escriba una contraseña que tenga 14 o 15 caracteres. Asegúrese de que la contraseña contiene 3 de 4 caracteres en mayúsculas, minúsculas, números y caracteres especiales.

3. Confirme la contraseña.

4. Haga clic en **Guardar** en la parte inferior de la página.

La contraseña de StorSimple Snapshot Manager se ha actualizado y se puede usar al autenticar hosts de Windows.

#### Cambio de la contraseña del administrador de dispositivos

Cuando se utiliza la interfaz de Windows PowerShell para tener acceso al dispositivo virtual, se le pedirá que escriba una contraseña de administrador de dispositivos. Por seguridad de sus datos, es necesario cambiar esta contraseña para poder usar el dispositivo virtual.

Realice los pasos siguientes para cambiar la contraseña del administrador de dispositivos para el dispositivo virtual de StorSimple.

1. En el dispositivo virtual, vaya a **Dispositivos > Configurar**.
 
2. Desplácese hacia abajo a la sección **Contraseña de administrador de dispositivos**. Proporcione una contraseña de administrador que tenga entre 8 y 15 caracteres. La contraseña debe contener 3 de 4 caracteres en mayúsculas, minúsculas, números y caracteres especiales.

3. Confirme la contraseña.
 
4. Haga clic en **Guardar** en la parte inferior de la página.

Ahora debe actualizarse la contraseña del administrador de dispositivos. Utilizará esta contraseña modificada para tener acceso a la interfaz de Windows PowerShell en el dispositivo virtual.

#### Configuración de la administración remota (opcional)

El acceso remoto a su dispositivo virtual mediante la interfaz de Windows PowerShell no está habilitado de forma predeterminada. Deberá habilitar primero la administración remota en el dispositivo virtual y, a continuación, habilitarlo en el cliente que se utilizará para acceder al dispositivo virtual.

Puede conectarse a través de HTTP o HTTPS. Por motivos de seguridad, se recomienda utilizar HTTPS con un certificado autofirmado para conectarse a su dispositivo virtual.

Realice los pasos siguientes para configurar la administración remota para el dispositivo virtual de StorSimple.


1. En el dispositivo virtual, vaya a **Dispositivos > Configurar**.

2. Desplácese hacia abajo a la sección **Administración remota**.

3. Establezca **Habilitar administración remota** en **Sí**.

4. Ahora puede elegir conectarse con HTTP. El valor predeterminado es conectarse a través de HTTPS. La conexión a través de HTTP solo es aceptable en redes de confianza.

5. Haga clic en **Descargar certificado de administración remota** para descargar un certificado de administración remota. Deberá especificar una ubicación para guardar este archivo. A continuación, este certificado debe estar instalado en el equipo cliente o host que va a utilizar para conectarse al dispositivo virtual.

6. Haga clic en **Guardar** en la parte inferior de la página.


## Trabajo con el dispositivo virtual de StorSimple

Ahora que ha creado y configurado el dispositivo virtual de StorSimple, está listo para comenzar a trabajar con él. Puede trabajar con contenedores de volúmenes, volúmenes y directivas de copia de seguridad en un dispositivo virtual como lo haría en un dispositivo físico de StorSimple; la única diferencia es que necesita asegurarse de que selecciona el dispositivo virtual en la lista de dispositivos. Consulte las secciones siguientes para obtener instrucciones acerca de las tareas asociadas:


- [Contenedores de volúmenes](storsimple-manage-volume-containers.md)

- [Volúmenes](storsimple-manage-volumes.md)

- [Directivas de copia de seguridad](storsimple-manage-backup-policies.md)

Las secciones siguientes describen algunas de las diferencias que surgen al trabajar con el dispositivo virtual.

### Mantenimiento de un dispositivo virtual de StorSimple

Dado que es un dispositivo solo de software, el mantenimiento del dispositivo virtual es mínimo en comparación con el mantenimiento del dispositivo físico. Tiene las siguientes opciones:

- **Actualizaciones de software**: puede ver la fecha en que se actualizó por última vez el software, junto con cualquier actualización de los mensajes de estado. Puede utilizar el botón **Buscar actualizaciones** en la parte inferior de la página para realizar una búsqueda manual si desea comprobar si hay nuevas actualizaciones. Si hay actualizaciones disponibles, haga clic en **Instalar actualizaciones** para instalar. Puesto que solo hay una única interfaz en el dispositivo virtual, significa que habrá una pequeña interrupción del servicio cuando se apliquen las actualizaciones. El dispositivo virtual se apagará automáticamente y se reiniciará (si es necesario) para aplicar las actualizaciones que se han publicado.
- **Paquete de soporte**: puede crear y cargar un paquete de soporte para ayudar al Soporte de Microsoft a solucionar problemas con el dispositivo virtual.

### Cuentas de almacenamiento para un dispositivo virtual

Las cuentas de almacenamiento se crean para su uso por el servicio StorSimple Manager, el dispositivo virtual y el dispositivo físico. Al crear las cuentas de almacenamiento, se recomienda usar un identificador de región en el nombre descriptivo para ayudar a garantizar que la región sea coherente en todos los componentes del sistema. Para un dispositivo virtual, es importante que todos los componentes estén en la misma región para evitar problemas de rendimiento.

### Desactivación de un dispositivo virtual de StorSimple

Desactivar un dispositivo virtual elimina la máquina virtual y los recursos que se crean cuando se aprovisionaron los servicios. Después de desactivar el dispositivo virtual, no se puede restaurar a su estado anterior. Antes de desactivar el dispositivo virtual, asegúrese de detener o eliminar clientes y hosts que dependen de él.

Desactivar un dispositivo virtual genera las siguientes acciones:

- Se quita el dispositivo virtual.

- Se quitan los OSDisk y los discos de datos creados para el dispositivo virtual.

- Se mantienen el servicio hospedado y la red virtual creados durante el aprovisionamiento. Si no los utiliza, debe eliminarlos manualmente.

- Se conservan las instantáneas de nube creadas para el dispositivo virtual.

Tan pronto como se muestra el dispositivo como desactivado en la página de servicio de StorSimple Manager, puede eliminar el dispositivo virtual de la lista de dispositivos del servicio StorSimple Manager.

### Acceso remoto a un dispositivo virtual de StorSimple

Una vez habilitado en la página de configuración del dispositivo StorSimple, puede usar la comunicación remota de Windows PowerShell para conectarse al dispositivo virtual desde otra máquina virtual dentro de la misma red virtual; por ejemplo, puede conectarse desde la máquina virtual del host que ha configurado y utilizado para conectarse a iSCSI. En la mayoría de las implementaciones, ya habrá abierto un punto de conexión público para tener acceso a su máquina virtual host que se puede utilizar para tener acceso al dispositivo virtual.

>[AZURE.WARNING]**Para mayor seguridad, se recomienda utilizar HTTPS al conectarse a los puntos de conexión y, a continuación, eliminar los puntos de conexión después de haber completado la sesión remota de PowerShell.**

Debe seguir los procedimientos que aparecen en [Conexión remota a su dispositivo StorSimple](storsimple-remote-connect.md) para establecer la comunicación remota para el dispositivo virtual.

Sin embargo, si desea conectarse directamente al dispositivo virtual desde otro equipo fuera de la red virtual o fuera del entorno de Microsoft Azure, necesitará crear puntos de conexión adicionales, como se describe en el procedimiento siguiente.

Realice los pasos siguientes para crear un punto de conexión público en el dispositivo virtual.

1. Inicie sesión en el portal clásico de Azure.

- Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual que se utiliza como dispositivo virtual.

- Haga clic en **Puntos de conexión**. En la página Puntos de conexión aparecen todos los extremos para la máquina virtual.

- Haga clic en **Agregar**. Aparecerá el cuadro de diálogo Agregar Puntos de conexión. Haga clic en la flecha para continuar.

- Para el **Nombre**, escriba el siguiente nombre para el Puntos de conexión: **WinRMHttps**.

- En **Protocolo**, especifique **TCP**.

- En **Puerto público**, escriba los números de puerto que desea usar para la conexión.

- Para el **puerto privado**, escriba **5986**.

- Haga clic en la marca de verificación para crear el punto de conexión.

Una vez creado el punto de conexión, puede ver sus detalles para determinar la dirección IP virtual (VIP) pública. Anote esta dirección.

Se recomienda conectarse desde otra máquina virtual dentro de la misma red virtual, ya que esta práctica minimiza el número de puntos de conexión públicos de la red virtual. Cuando utiliza este método, simplemente conéctese a la máquina virtual mediante una sesión de escritorio remoto y, a continuación, configure esa máquina virtual para su uso como haría con cualquier otro cliente de Windows en una red local. No es necesario anexar el número de puerto público porque ya se conocerá el puerto.

### Detención y reinicio

A diferencia del dispositivo físico de StorSimple, no hay que presionar ningún botón de encendido o apagado en un dispositivo virtual de StorSimple. Sin embargo, puede haber ocasiones en las que necesite detener y reiniciar el dispositivo virtual. Por ejemplo, algunas actualizaciones pueden requerir que se reinicie la máquina virtual para finalizar el proceso de actualización. La manera más fácil para iniciar, detener y reiniciar un dispositivo virtual es utilizar la consola de administración de máquinas virtuales.

Cuando observa la consola de administración, el estado del dispositivo virtual es **En ejecución** porque se inicia de forma predeterminada después de crearlo. Puede detener y reiniciar una máquina virtual en cualquier momento.

Para detener un dispositivo virtual, haga clic en su nombre y, a continuación, haga clic en **Apagar**. Mientras se está cerrando el dispositivo virtual, su estado es **Deteniéndose**. Una vez detenido el dispositivo virtual, su estado es **Detenido**.

Cuando se ejecuta un dispositivo virtual y desea reiniciarlo, haga clic en su nombre y, a continuación, haga clic en **Reiniciar**. Mientras se reinicia el dispositivo virtual, su estado es **Reiniciando**. Cuando el dispositivo virtual está listo para su uso, su estado es **En ejecución**.

También puede utilizar los siguientes cmdlets de Windows PowerShell para iniciar, detener y reiniciar el dispositivo virtual. Después de cada de cmdlet hay un ejemplo.

`Start-AzureVMC:\PS>Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    

`Stop-AzureVMC:\PS>Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Restart-AzureVMC:\PS>Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

### Restablecimiento de los valores predeterminados de fábrica

Si decide que desea empezar de nuevo con su dispositivo virtual, simplemente desactívelo y elimínelo y, a continuación, cree uno nuevo. Al igual que cuando se restablece el dispositivo físico, el dispositivo virtual nuevo no tendrá las actualizaciones instaladas; por lo tanto, asegúrese de comprobar si hay actualizaciones antes de usarlo.


## Conmutación por error en el dispositivo virtual

La recuperación ante desastres (DR) es uno de los escenarios clave para los que se diseñó el dispositivo virtual de StorSimple. En este escenario, es posible que el dispositivo físico de StorSimple o todo el centro de datos no esté disponible. Afortunadamente, puede usar un dispositivo virtual para restaurar las operaciones en una ubicación alternativa. Durante la recuperación ante desastres, los contenedores de volúmenes del dispositivo de origen cambia la propiedad y se transfieren al dispositivo virtual. Los requisitos previos para la recuperación ante desastres son que haya creado y configurado el dispositivo virtual, todos los volúmenes en el contenedor de volúmenes se hayan desconectado y el contenedor de volúmenes tenga asociada una instantánea en la nube.

>[AZURE.NOTE]No se puede efectuar la conmutación por error ni clonar desde un dispositivo que ejecute Update 1 para un dispositivo que ejecute software anterior a la actualización 1. Si selecciona un dispositivo de destino que ejecute software anterior a Update 1, se le notificará que deberá actualizar el dispositivo de destino antes de realizar la conmutación por error.

### Para restaurar el dispositivo físico en el dispositivo virtual de StorSimple

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.

2. Abra la página **Dispositivo** y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

3. Seleccione un contenedor de volúmenes que le gustaría conmutar por error en el dispositivo virtual. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes dentro del contenedor. Seleccione un volumen y haga clic en **Desconectado** para desconectar el volumen. Repita este proceso para todos los volúmenes del contenedor de volúmenes.

4. Repita el paso anterior para todos los contenedores de volúmenes que desee conmutar por error en el dispositivo virtual.

5. En la página **Dispositivo**, seleccione el dispositivo que necesita conmutar por error y, a continuación, haga clic en **Conmutación por error** para abrir el asistente **Dispositivo de conmutación por error**.

6. En **Elegir el contenedor de volúmenes para la conmutación por error**, seleccione los contenedores de volumen que le gustaría conmutar por error. Para aparecer en esta lista, el contenedor de volúmenes debe contener una instantánea en la nube y desconectado. Si un contenedor de volúmenes que esperaba ver no está presente, cancele al asistente y compruebe que está desconectado.

7. En la siguiente página, en **Elegir un dispositivo de destino para los volúmenes** en los contenedores seleccionados, seleccione el dispositivo virtual en la lista desplegable de dispositivos disponibles. Solo los dispositivos que tienen la capacidad disponible se muestran en la lista.

8. Revise la configuración de conmutación por error en la página **Confirmar conmutación por error**. Si son correctos, haga clic en el icono de verificación.

Comenzará el proceso de conmutación por error. Cuando finalice la conmutación por error, vaya a la página de dispositivos y seleccione el dispositivo virtual que se utiliza como destino para el proceso de conmutación por error. Vaya a la página Contenedores de volúmenes. Deben aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

>[AZURE.NOTE]La cantidad de almacenamiento admitida en el dispositivo virtual es de 30 TB.

## Cerrar o eliminar el dispositivo virtual

Si ha configurado y usado previamente un dispositivo virtual de StorSimple, pero ahora desea detener la acumulación de cargos para su uso por el proceso, puede apagar el dispositivo virtual. Apagar el dispositivo virtual no elimina su sistema operativo ni los discos de datos del almacenamiento. Detiene el cargo acumulado en su suscripción, pero seguirán los cargos de almacenamiento del sistema operativo y los discos de datos.

Si elimina o apaga el dispositivo virtual, aparecerá como **Desconectado** en la página de dispositivos del servicio StorSimple Manager. Puede desactivarlo o eliminarlo como un dispositivo si desea eliminar las copias de seguridad creadas por el dispositivo virtual. Para más información, consulte [Desactivar y eliminar un dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

### Para apagar el dispositivo virtual de StorSimple

1. Inicie sesión en el portal clásico de Azure.

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione el dispositivo virtual.

3. Haga clic en **Apagar**.

### Para eliminar el dispositivo virtual de StorSimple

1. Inicie sesión en el portal clásico de Azure.

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione el dispositivo virtual.

3. Haga clic en **Eliminar** y elija eliminar todos los discos de la máquina virtual.

## Pasos siguientes

Obtenga información sobre cómo [restaurar un volumen de StorSimple de un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md).

<!---HONumber=AcomDC_0121_2016-->