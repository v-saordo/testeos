<properties
    pageTitle="Uso del complemento subordinado de Azure con Jenkins Continuous Integration | Microsoft Azure"
    description="Describe el uso del complemento subordinado de Azure con Jenkins Continuous Integration"
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="" />

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="01/09/2016"
    ms.author="robmcm"/>

# Uso del complemento subordinado de Azure con Jenkins Continuous Integration

Puede usar el complemento subordinado de Azure para Jenkins para aprovisionar los nodos subordinados en Azure cuando se ejecutan compilaciones distribuidas.

## Para instalar el complemento subordinado de Azure
1. En el panel de Jenkins, haga clic en **Manage Jenkins** (Administrar Jenkins).
2. En la página **Manage Jenkins** (Administrar Jenkins), haga clic en **Manage Plugins** (Administrar complementos).
3. Haga clic en la pestaña **Available** (Disponible).
4. En el campo de filtro situado encima de la lista de complementos disponibles, escriba **Azure** para limitar la lista de complementos relevantes.

	Si opta por desplazarse por la lista de complementos disponibles, encontrará el complemento subordinado de Azure en la sección **Cluster Management and Distributed Build** (Administración de clústeres y compilación distribuida).

5. Active la casilla **Azure Slave Plugin** (Complemento esclavo de Azure).
6. Haga clic en **Install without restart** (Instalar sin reiniciar) o **Download now and install after restart** (Descargar ahora e instalar después de reiniciar).

Ahora que está instalado el complemento, los siguientes pasos son configurarlo con el perfil de suscripción de Azure y crear una plantilla que se usará en la creación de la máquina virtual para el nodo subordinado.


## Para configurar el complemento esclavo de Azure con el perfil de suscripción

Un perfil de suscripción, también conocido como configuración de publicación, es un archivo XML que contiene credenciales seguras y alguna información adicional que necesitará para trabajar con Azure en el entorno de desarrollo. Para configurar el complemento esclavo de Azure, necesitará:

* su id. de suscripción
* un certificado de administración para la suscripción

Estos se pueden encontrar en su perfil de suscripción. Si no tiene una copia de su perfil de suscripción, puede descargarlo del [sitio de suscripción](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0). A continuación se muestra un ejemplo de un perfil de suscripción.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID value>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Una vez que tiene el perfil de suscripción, siga estos pasos para configurar el complemento esclavo de Azure:

1. En el panel de Jenkins, haga clic en **Manage Jenkins** (Administrar Jenkins).
2. Haga clic en **Configure System** (Configurar sistema).
3. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).
4. Haga clic en **Add new cloud > Microsoft Azure** (Agregar nueva nube > Microsoft Azure).

	![sección de nube](./media/azure-slave-plugin-for-jenkins/jenkins-cloud-section.png)

	Se mostrarán los campos donde debe especificar los detalles de suscripción.

	![configuración de la suscripción](./media/azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png)

5. Copie los valores de certificado de administración e id. de suscripción del perfil de suscripción y péguelos en los campos correspondientes.

	Al copiar el certificado de administración y el id. de suscripción, no incluya las comillas que delimitan los valores.

6. Haga clic en **Verify configuration** (Comprobar configuración).
7. Cuando se verifique que la configuración es correcta, haga clic en **Save** (Guardar).

## Para configurar una plantilla de máquina virtual para el complemento esclavo de Azure

Una plantilla de máquina virtual define los parámetros que usará el complemento para crear un nodo esclavo en Azure. En los pasos siguientes crearemos plantilla para una máquina virtual de Ubuntu.

1. En el panel de Jenkins, haga clic en **Manage Jenkins** (Administrar Jenkins).
2. Haga clic en **Configure System** (Configurar sistema).
3. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).

4. Dentro de la sección **Cloud** (Nube), busque **Add Azure Virtual Machine Template** (Agregar plantilla de máquina virtual de Azure) y haga clic en **Add** (Agregar).

	![agregar plantilla de vm](./media/azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png)

	Esta acción mostrará los campos donde se especifican los detalles de la plantilla que está creando.

	![configuración general en blanco](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png)

5. Especifique un nombre de servicio en la nube de Azure en el campo **Name** (Nombre). Si el nombre que especifique se refiere a un servicio en la nube existente, la máquina virtual se aprovisionará en dicho servicio. De lo contrario, Azure creará uno nuevo.

6. En el cuadro **Description** (Descripción), escriba el texto que describe la plantilla que está creando. Esta información es solo para registros y no se usa en el aprovisionamiento de una máquina virtual.
7. El campo **Labels** (Etiquetas) se usa para identificar la plantilla que está creando y posteriormente para hacer referencia a la plantilla al crear un trabajo de Jenkins. Para nuestro propósito, escriba **linux** en este cuadro.
8. En el lista **Region** (Región), haga clic en la región donde se creará la máquina virtual.
9. En el lista **Virtual Machine Size** (Tamaño de máquina virtual), haga clic en el tamaño adecuado.
10. En el cuadro **Storage Account Name** (Nombre de cuenta de almacenamiento), especifique una cuenta de almacenamiento donde se creará la máquina virtual. Asegúrese de que se encuentra en la misma región que el servicio en la nube que se va a usar. Si desea crear un nuevo almacenamiento, puede dejar este cuadro en blanco.
11. El tiempo de retención especifica el número de minutos antes de que Jenkins elimine un subordinado inactivo. Deje el valor predeterminado de 60. También puede optar por apagar el subordinado en lugar de eliminarlo cuando está inactivo. Para ello, seleccione la casilla **Shutdown Only (Do Not Delete) After Retention Time** (Solo apagar [no eliminar] después del tiempo de retención).
12. En la lista **Usage** (Uso), haga clic en la condición adecuada de uso de este nodo subordinado. Por ahora, haga clic en **Utilize this node as much as possible** (Usar este nodo tanto como sea posible).

	En este punto, su formulario sería parecido al siguiente:

	![configuración de la plantilla general de punto de comprobación](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png)

	El siguiente paso sería proporcionar detalles sobre la imagen de sistema operativo en la que desea crear su subordinado.

13. En el cuadro **Image Family or Id** (Familia o identificador de imagen), debe especificar la imagen del sistema que se instalará en la máquina virtual. Puede seleccionar de una lista de familias de imagen o especificar una imagen personalizada.

	Si desea seleccionar de una lista de familias de imagen, escriba el primer carácter (distingue mayúsculas de minúsculas) del nombre de familia de imagen. Por ejemplo, al escribir **U** aparecerá una lista de familias de Ubuntu Server. Una vez que seleccione de la lista, Jenkins usará la versión más reciente de esa imagen del sistema de esa familia al aprovisionar la máquina virtual.

	![ejemplo de lista de imágenes de sistema operativo](./media/azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png)

	Si tiene una imagen personalizada que desea usar en su lugar, escriba su nombre. Los nombres de imagen personalizada no se muestran en una lista, por lo que debe asegurarse de escribir el nombre correctamente.

	Para este tutorial, escriba **U** para mostrar una lista de imágenes de Ubuntu y haga clic en **Ubuntu Server 14.04 LTS**.

14. En la lista **Launch Method** (Método de inicio), haga clic en **SSH**.
15. Copie el siguiente script y péguelo en el cuadro **Init script** (Script de inicialización).

		# Install Java
		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk



		# Install git

		sudo apt-get install -y git



		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

	El script de inicialización se ejecutará después de crearse la máquina virtual. En este ejemplo, el script instala Java, git y ant.

16. En los cuadros **Username** (Nombre de usuario) y **Password** (Contraseña), escriba los valores preferidos para la cuenta de administrador que se creará en la máquina virtual.
17. Haga clic en **Verify Template** (Comprobar plantilla) para comprobar si los parámetros especificados son válidos.
18. Haga clic en **Guardar**.


## Para crear un trabajo de Jenkins que se ejecuta en un nodo subordinado en Azure

En esta sección, creará una tarea de Jenkins que se ejecutará en un nodo subordinado en Azure. Deberá tener su propio proyecto activo en GitHub para continuar.

1. En el panel de Jenkins, haga clic en **New Item** (Nuevo elemento).
2. Escriba un nombre para la tarea que se va a crear.
3. Para el tipo de proyecto, haga clic en **Freestyle project** (Proyecto estilo libre).
4. Haga clic en **Aceptar**.
5. En la página de configuración de la tarea, seleccione **Restrict where this project can be run** (Restringir dónde se puede ejecutar este proyecto).
6. En el cuadro **Label Expression** (Expresión de etiqueta), escriba **linux**. En la sección anterior, creamos una plantilla subordinada que llamamos **linux** y que es la que vamos a especificar aquí.
7. En la sección **Build** (Compilar), haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Execute shell** (Ejecutar shell).
8. Edite el siguiente script; para ello, sustituya **(el nombre de la cuenta de github)**, **(el nombre del proyecto)** y **(el directorio del proyecto)** por los valores adecuados y pegue el script editado en el área de texto que aparece.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your GitHub account name)/(your project name).git

		fi

		# change directory to project

		cd $currentDir/(your project directory)



		#Execute build task

		ant

9. Haga clic en **Guardar**.
10. En el panel de Jenkins, mantenga el mouse sobre la tarea que acaba de crear y haga clic en la flecha hacia abajo para mostrar las opciones de la tarea.
11. Haga clic en **Build now** (Compilar ahora).

Jenkins creará luego un nodo subordinado con la plantilla que creó en la sección anterior y ejecutará el script especificado en el paso de compilación de esta tarea.

<!---HONumber=AcomDC_0224_2016-->