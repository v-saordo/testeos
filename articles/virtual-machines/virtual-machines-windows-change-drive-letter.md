<properties
	pageTitle="Conversión de la unidad D de una máquina virtual en un disco de datos | Microsoft Azure"
	description="Describe cómo cambiar las letras de unidad en una máquina virtual de Windows creada con el modelo de implementación clásica a fin de poder usar la unidad D: como unidad de datos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="cynthn"/>

# Uso de la unidad de disco D como unidad de datos en una máquina virtual de Windows 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Si necesita utilizar la unidad D para almacenar datos, siga estas instrucciones para usar una unidad distinta para el disco temporal. Nunca use el disco temporal para almacenar los datos que desee conservar.

## Conectar el disco de datos

En primer lugar, deberá conectar el disco de datos a la máquina virtual. Para hacerlo, consulte [Conexión de un disco de datos a una máquina virtual de Windows][Attach].

Si desea usar un disco de datos existente, asegúrese de que también se haya cargado el VHD en la cuenta de almacenamiento. Para obtener instrucciones, consulte los pasos 3 y 4 que se encuentran en [Creación y carga de un VHD de Windows Server en Azure][VHD].


## Mover temporalmente el archivo pagefile.sys a la unidad C

1. Conexión a una máquina virtual. 

2. Haga clic con el botón derecho en el menú **Inicio** y seleccione **Sistema**.

3. En el menú que aparece a la izquierda, seleccione **Configuración avanzada del sistema**.

4. En la sección **Rendimiento**, seleccione **Configuración**.

5. Seleccione la pestaña **Opciones avanzadas**.

5. En la sección **Memoria virtual**, seleccione **Cambiar**.

6. Seleccione la unidad **C**, luego haga clic en **Tamaño administrado por el sistema** y, a continuación, haga clic en **Establecer**.

7. Seleccione la unidad **D**, luego haga clic en **Sin archivo de paginación** y, a continuación, haga clic en **Establecer**.

8. Haga clic en Aplicar. Aparecerá una advertencia que indica que se debe reiniciar el equipo para que los cambios entren en vigor.

9. Reinicie la máquina virtual.




## Cambiar las letras de las unidades 

1. Una vez que se reinicia la máquina virtual, vuelva a iniciar sesión en ella.

2. Haga clic en el menú **Inicio**, escriba **diskmgmt.msc** y presione Entrar. Se iniciará la Administración de discos.

3. Haga clic con el botón derecho en **D**, la unidad de almacenamiento temporal, y seleccione **Cambiar la letra y rutas de acceso de unidad**.

4. En Letra de unidad, seleccione la unidad **G** y haga clic en **Aceptar**.

5. Haga clic con el botón derecho en el disco de datos y seleccione **Cambiar la letra y rutas de acceso de unidad**.

6. En Letra de unidad, seleccione la unidad **D** y haga clic en **Aceptar**.

7. Haga clic con el botón derecho en **G**, la unidad de almacenamiento temporal, y seleccione **Cambiar la letra y rutas de acceso de unidad**.

8. En Letra de unidad, seleccione la unidad **E** y haga clic en **Aceptar**.

> [AZURE.NOTE]Si la máquina virtual tiene otros discos o unidades, use el mismo método para reasignar las letras de unidad de los demás discos y unidades. La configuración de disco que desea es la siguiente: - C: disco del sistema operativo - D: disco de datos - E: disco temporal



## Devolver el archivo pagefile.sys a la unidad de almacenamiento temporal 

1. Haga clic con el botón derecho en el menú **Inicio** y seleccione **Sistema**.

2. En el menú que aparece a la izquierda, seleccione **Configuración avanzada del sistema**.

3. En la sección **Rendimiento**, seleccione **Configuración**.

4. Seleccione la pestaña **Opciones avanzadas**.

5. En la sección **Memoria virtual**, seleccione **Cambiar**.

6. Seleccione la unidad del sistema operativo **C**, haga clic en **Sin archivo de paginación** y, a continuación, haga clic en **Establecer**.

7. Seleccione la unidad de almacenamiento temporal **E**, haga clic en **Tamaño administrado por el sistema** y, a continuación, haga clic en **Establecer**.

8. Haga clic en **Apply**. Aparecerá una advertencia que indica que se debe reiniciar el equipo para que los cambios entren en vigor.

9. Reinicie la máquina virtual.




## Recursos adicionales
[Inicio de sesión en una máquina virtual con Windows Server][Logon]

[Desacoplamiento de un disco de datos de una máquina virtual de Windows][Detach]

[Acerca de las cuentas de almacenamiento de Azure][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Nov15_HO2-->