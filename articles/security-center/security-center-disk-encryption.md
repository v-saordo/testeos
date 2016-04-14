<properties
   pageTitle="Cifrado de una máquina virtual de Azure | Microsoft Azure"
   description="Este documento le ayuda a cifrar una máquina virtual de Azure después de recibir una alerta de Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/03/2016"
   ms.author="tomsh"/>

# Cifrado de una máquina virtual de Azure
Azure Security Center le avisará si tiene máquinas virtuales que no estén cifradas. Estas alertas se mostrarán con gravedad alta y se recomienda cifrar estas máquinas virtuales.

![Recomendación de cifrado de disco](./media/security-center-disk-encryption\security-center-disk-encryption-fig1.png)

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

Para cifrar las máquinas virtuales de Azure que Azure Security Center ha identificado que lo necesitan, se recomienda lo siguiente:

- Instale y configure Azure PowerShell. Esto permitirá ejecutar los comandos de PowerShell para configurar los requisitos previos necesarios para cifrar las máquinas virtuales de Azure.
- Obtenga y ejecute el script Azure Disk Encryption Prerequisite Setup de Azure PowerShell.
- Cifre las máquinas virtuales.

El objetivo de este documento es que pueda cifrar máquinas virtuales aunque tenga pocas nociones, o ninguna, de Azure PowerShell. En este documento se considera que utiliza Windows 10 como el equipo cliente desde el que va a configurar el Cifrado de discos de Azure.
 
Existen varios enfoques que pueden usarse para configurar los requisitos previos y el cifrado para máquinas virtuales de Azure. Si conoce bien Azure PowerShell o CLI de Azure, puede preferir el uso de métodos alternativos.

> [AZURE.NOTE] Para más información acerca de métodos alternativos para configurar el cifrado para máquinas virtuales de Azure, consulte [Azure Disk Encryption for Windows and Linux Azure Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) (Cifrado de discos de Azure para máquinas virtuales de Windows y Linux).

## Instale y configure Azure PowerShell.
Debe tener Azure PowerShell versión 1.2.1 o superior instalado en el equipo. El artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) contiene todos los pasos necesarios para aprovisionar el equipo para que funcione con Azure PowerShell. El enfoque más sencillo es usar el método Instalador de plataforma web mencionado en dicho artículo. Aunque Azure PowerShell ya esté instalado, instálelo de nuevo con el enfoque del Instalador de plataforma web para tener la versión más reciente.


## Obtención y ejecución del script Azure Disk Encryption Prerequisite Setup
El script Azure Disk Encryption Prerequisites Configuration configurará todos los requisitos previos necesarios para cifrar las máquinas virtuales de Azure.

1.	Vaya a la página de GitHub que tiene el [script Azure Disk Encryption Prerequisite Setup](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
2.	En la página GibHub, haga clic en el botón **Raw** (Sin formato).
3.	Utilice **CTRL-A** para seleccionar todo el texto de la página y, a continuación, **CTRL-C** para copiarlo en el Portapapeles.
4.	Abra el **Bloc de notas** y pegue el texto copiado en el Bloc de notas. 
5.	Cree una nueva carpeta en la unidad C: denominada **AzureADEScript**.
6.	Guarde el archivo del Bloc de notas, haga clic en **Archivo** y, después, en **Guardar como**. En el cuadro de texto Nombre de archivo, escriba **"ADEPrereqScript.ps1"** y haga clic en **Guardar** (asegúrese de colocar comillas antes y después del nombre; de lo contrario, el archivo se guardará con una extensión de archivo .txt). 

Ahora que el contenido del script se ha guardado, abra el script en el PowerShell ISE:

1.	En el menú Inicio, haga clic en **Cortana**. Para solicitar "PowerShell" a **Cortana**, escriba **PowerShell** en el cuadro de texto de búsqueda de Cortana. 
2.	Haga clic con el botón derecho en **Windows PowerShell ISE** y haga clic en **Ejecutar como administrador**.
3.	En la ventana **Administrador: Windows PowerShell ISE**, haga clic en **Ver** y, a continuación, en **Mostrar panel de scripts**. 
4.	Si ve el panel **Comandos** en la parte derecha de la ventana, haga clic en la **"x"** en la esquina superior derecha del panel para cerrarlo. Si el texto es demasiado pequeño para verlo, use **CTRL+Más** ("Más" es el signo "+"). Si el texto es demasiado grande, use **CTRL+Menos** (Menos es el signo "-"). 
5.	Haga clic en **Archivo** y, a continuación, en **Abrir**. Vaya a la carpeta **C:\\AzureADEScript** y haga doble clic en **ADEPrereqScript**.
6.	El contenido de **ADEPrereqScript** debería aparecer ahora en PowerShell ISE, codificado por colores para ayudarle a ver más fácilmente los diversos componentes, como comandos, parámetros y variables. 

Ahora debería ver algo parecido a la siguiente ilustración.

![Ventana de PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig2.png)

El panel superior se denomina "panel de scripts" y el inferior "consola". Estos términos se utilizarán más adelante en este artículo.

## Ejecución del comando de PowerShell Azure Disk Encryption Prerequisite Setup

El script Azure Disk Encryption Prerequisite Setup pedirá la siguiente información después de iniciarlo:

- **Nombre del grupo de recursos**: nombre del grupo de recursos en el que desea colocar el Almacén de claves. Si aún no hay ninguno, se creará un grupo de recursos con este nombre. Si ya tiene un grupo de recursos que desea usar en esta suscripción, escriba su nombre.
- **Nombre del Almacén de claves**: nombre del Almacén de claves en el que se colocarán las claves de cifrado. Si aún no hay ninguno, se creará un Almacén de claves con este nombre. Si ya tiene un Almacén de claves que desea usar, escriba su nombre.
- **Ubicación**: ubicación del Almacén de claves. Asegúrese de que el Almacén de claves y las máquinas virtuales que se van a cifrar están en la misma ubicación. Si no conoce la ubicación, más adelante en este artículo hay pasos que le mostrarán cómo averiguarla. 
- **Nombre de aplicación de Azure Active Directory**: nombre de la aplicación de Azure Active Directory que se usará para escribir secretos en el Almacén de claves. Si no existe, se creará una aplicación con este nombre. Si ya tiene una aplicación de Azure Active Directory que desea usar, escriba su nombre.

> [AZURE.NOTE] Si tiene curiosidad sobre por qué necesita para crear una aplicación de Azure Active Directory, consulte la sección *Registro de una aplicación con Azure Active Directory* del artículo [Introducción al Almacén de claves de Azure](../key-vault/key-vault-get-started.md).

Realice los pasos siguientes para cifrar una máquina virtual de Azure:

1.	Si ha cerrado PowerShell ISE, abra una instancia con privilegios elevados de PowerShell ISE. Siga las instrucciones anteriores de este artículo si PowerShell ISE aún no está abierto. Si cierra el script, abra **ADEPrereqScript.ps1**; para ello, haga clic en **Archivo**, **Abrir** y seleccione el script en la carpeta **c:\\AzureADEScript**. Si ha seguido este artículo desde el principio, simplemente vaya al paso siguiente. 
2.	En la consola de PowerShell ISE (el panel inferior de PowerShell ISE), cambie el foco a la versión local del script; para ello, escriba **cd c:\\AzureADEScript** y presione **ENTRAR**.
3.	Configure la directiva de ejecución en la máquina para poder ejecutar el script. Escriba **Set-ExecutionPolicy Unrestricted** en la consola y presione ENTRAR. Si ve un cuadro de diálogo que indica los efectos del cambio de la directiva de ejecución, haga clic en **Sí a todo** o **Sí** (si ve **Sí a todo**, seleccione esta opción; si no ve **Sí a todo**, haga clic en **Sí**). 
4.	Inicie sesión en la cuenta de Azure. En la consola, escriba **Login-AzureRmAccount** y presione **ENTRAR**. Aparecerá un cuadro de diálogo en el que escribir sus credenciales (asegúrese de que tiene derechos para cambiar las máquinas virtuales; de lo contrario, no podrá cifrarlos. Si no está seguro, pregunte al administrador o propietario de la suscripción). Debería ver la información acerca de **Environment** (Entorno), **Account** (Cuenta), **TenantId** (Identificador de inquilino), **SubscriptionId** (Identificador de suscripción) y **CurrentStorageAccount** (Cuenta de almacenamiento actual). Copie el valor de **SubscriptionId** en el Bloc de notas. Lo necesitará en el paso 6.
5.	Busque a qué suscripción pertenece la máquina virtual y su ubicación. Vaya a [https://portal.azure.com](ttps://portal.azure.com) e inicie sesión. En la parte izquierda de la página, haga clic en **Máquinas virtuales**. Verá una lista de las máquinas virtuales y las suscripciones a las que pertenecen.

	![Máquinas virtuales](./media/security-center-disk-encryption\security-center-disk-encryption-fig3.png)

6.	Vuelva a PowerShell ISE. Establezca el contexto de la suscripción en la que se va a ejecutar el script. En la consola, escriba **Select-AzureRmSubscription –SubscriptionId<your_subscription_Id>** (reemplace **< your_subscription_Id >** por su identificador de suscripción real) y presione **ENTRAR**. Verá la información acerca de Environment (Entorno), **Account** (Cuenta), **TenantId** (Identificador de inquilino), **SubscriptionId** (Identificador de suscripción) y **CurrentStorageAccount** (Cuenta de almacenamiento actual).
7.	Ya está preparado para ejecutar el script. Haga clic en el botón **Ejecutar script** o presione **F5** en el teclado.

	![Ejecución del script de PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig4.png)

8.	El script solicita un valor para **resourceGroupName:**; escriba el nombre del *Grupo de recursos* que desea usar y presione **ENTRAR**. Si no tiene ningún nombre, escriba el que desea usar para uno nuevo. Si ya tiene un *Grupo de recursos* que desea usar (por ejemplo, en el que está la máquina virtual), escriba su nombre.
9.	El script solicita un valor para **keyVaultName:**; escriba el nombre del *Almacén de claves* que desea usar y presione ENTRAR. Si no tiene ningún nombre, escriba el que desea usar para uno nuevo. Si ya tiene un Almacén de claves que desea usar, escriba el nombre del *Almacén de claves* existente.
10.	El script solicita un valor para **location:**; escriba el nombre de la ubicación donde se encuentra la máquina virtual que desea cifrar y presione **ENTRAR**. Si no recuerda la ubicación, vuelva al paso 5.
11.	El script solicita un valor para **aadAppName:**; escriba el nombre de la aplicación de *Azure Active Directory* que desea usar y presione **ENTRAR**. Si no tiene ningún nombre, escriba el que desea usar para uno nuevo. Si ya tiene un *aplicación de Azure Active Directory* que desea usar, escriba el nombre de la *aplicación de Azure Active Directory* existente.
12.	Aparece un cuadro de diálogo de inicio de sesión. Proporcione sus credenciales (sí, ya ha iniciado sesión una vez, pero ahora es necesario volver a hacerlo).
13.	El script se ejecuta y cuando finalice le pedirá que copie los valores de **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl** y **keyVaultResourceId**. Copie cada uno de estos valores en el Portapapeles y péguelo en el Bloc de notas. 
14.	Vuelva a PowerShell ISE, coloque el cursor al final de la última línea y presione **ENTRAR**.

La salida del script debe ser similar a la pantalla siguiente:

![Salida de PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig5.png)

## Cifrado de la máquina virtual de Azure 

Ahora está listo para cifrar la máquina virtual. Si la máquina virtual se encuentra en el mismo grupo de recursos que el Almacén de claves, puede ir a la sección de pasos de cifrado. Sin embargo, si la máquina virtual no está en el mismo grupo de recursos que el Almacén de claves, debe escribir lo siguiente en la consola de PowerShell ISE:

**$resourceGroupName = <’GR\_Máquina\_Virtual’>**

Reemplace **< Virtual_Machine_RG >** por el nombre del grupo de recursos en el que se encuentran las máquinas virtuales, entre comillas simples. A continuación, presione **ENTRAR**. Para confirmar que ha escrito el nombre del grupo de recursos correcto, escriba lo siguiente en la consola de PowerShell ISE:

**$resourceGroupName**

Presione **ENTRAR**. Debería ver el nombre del grupo de recursos en el que se encuentran las máquinas virtuales. Por ejemplo:

![Salida de PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig6.png)

### Pasos de cifrado

En primer lugar, debe indicar a PowerShell el nombre de la máquina virtual que desea cifrar. En la consola, escriba:

**$vmName = <’su\_nombre\_mv’>**

Reemplace **<'su\_nombre\_mv' >** por el nombre de la máquina virtual (asegúrese de que el nombre está rodeado por comillas simples) y presione **ENTRAR**.

Para confirmar que ha escrito el nombre correcto de la máquina virtual, escriba:

**$vmName**

Presione **ENTRAR**. Debería ver el nombre de la máquina virtual que desea cifrar. Por ejemplo:

![Salida de PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig7.png)

Hay dos maneras de ejecutar el comando de cifrado para cifrar la máquina virtual. El primer método consiste en escribir el siguiente comando en la consola de PowerShell ISE:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId
~~~

Después de escribir este comando, presione **ENTRAR**.

El segundo método es hacer clic en el panel de scripts (panel superior de PowerShell ISE) y desplácese hasta el final del script. Resalte el comando indicado antes, haga clic con el botón derecho en él y luego haga clic en **Ejecutar selección** o presione **F8** en el teclado.

![PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig8.png)

Independientemente del método que utilice, aparecerá un cuadro de diálogo que informa de que se tardará 10-15 minutos para completar la operación. Haga clic en **Sí**.

Mientras lleva a cabo el proceso de cifrado, puede volver al Portal de Azure y ver el estado de la máquina virtual. En la parte izquierda de la página, haga clic en **Máquinas virtuales** y, después, en la hoja **Máquinas virtuales**, haga clic en el nombre de la máquina virtual que está cifrando. En la hoja que aparece, observará que en **Estado** se dice que se está **Actualizando**. Esto demuestra que el cifrado está en proceso.

![Más información acerca de la máquina virtual](./media/security-center-disk-encryption\security-center-disk-encryption-fig9.png)

Vuelva a PowerShell ISE. Cuando se completa el script, verá lo que aparece en la ilustración siguiente.

![Salida de PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig10.png)

Para demostrar que la máquina virtual está ahora cifrada, vuelva al Portal de Azure y haga clic en **Máquinas virtuales** en la parte izquierda de la página. Haga clic en el nombre de la máquina virtual que ha cifrado. En la hoja **Configuración**, haga clic en **Discos**.

![Opciones de configuración](./media/security-center-disk-encryption\security-center-disk-encryption-fig11.png)

En la hoja **Discos**, verá que **Cifrado** está **Habilitado**.

![Propiedades de discos](./media/security-center-disk-encryption\security-center-disk-encryption-fig12.png)


## Pasos siguientes

En este documento, ha aprendido cómo cifrar una máquina virtual de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración de alertas de seguridad y respuesta a estas en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): Obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): Encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0309_2016-->