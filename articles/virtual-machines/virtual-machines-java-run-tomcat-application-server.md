<properties
	pageTitle="Tomcat en una máquina virtual | Microsoft Azure"
	description="En este tutorial se usan los recursos creados con el modelo de implementación clásico, y se muestra cómo crear una máquina virtual de Windows y configurarla para ejecutar el servidor de aplicaciones de Apache Tomcat."
	services="virtual-machines"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
    tags="azure-service-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="robmcm"/>

# Ejecución de un servidor de aplicaciones Java en una máquina virtual creada con el modelo de implementación clásico

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Con Azure, puede utilizar una máquina virtual para proporcionar funciones de servidor. Por ejemplo, una máquina virtual que se ejecuta en Azure se puede configurar para hospedar un servidor de aplicaciones Java, como Apache Tomcat. Al finalizar esta guía, adquirirá una comprensión de cómo crear una máquina virtual que se ejecute en Azure y configurarla para ejecutar un servidor de aplicaciones Java.

Aprenderá a:

* Crear una máquina virtual que tenga un kit de desarrollo de Java (JDK) ya instalado.
* Iniciar sesión de manera remota en la máquina virtual.
* Instalar un servidor de aplicaciones Java en la máquina virtual.
* Crear un extremo para la máquina virtual.
* Abrir un puerto en el firewall para el servidor de aplicaciones.

En este tutorial, se va a instalar un servidor de aplicaciones Apache Tomcat en una máquina virtual. La instalación completada dará como resultado una instalación de Tomcat como la siguiente.

![Máquina virtual que ejecuta Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Para crear una máquina virtual

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Nuevo**, **Proceso**, **Máquina virtual** y, a continuación, en **Desde la galería**.
3. En el cuadro de diálogo **Selección de imagen de máquina virtual**, seleccione **JDK 7 Windows Server 2012**. Tenga en cuenta que **JDK 6 Windows Server 2012** está disponible si tiene aplicaciones heredadas que aún no están preparadas para ejecutarse en JDK 7.
4. Haga clic en **Siguiente**.
5. En el cuadro de diálogo **Configuración de la máquina virtual**:
    1. Especifique un nombre para la máquina virtual.
    2. Especifique el tamaño que se va a utilizar para la máquina virtual.
    3. Escriba un nombre para el administrador en el campo **Nombre de usuario**. Recuerde este nombre y la contraseña que va a escribir a continuación ya que los usará cuando inicie sesión de forma remota en la máquina virtual.
    4. Escriba una contraseña en el campo **Contraseña nueva** y confírmela en el campo **Confirmar**. Esta es la contraseña de la cuenta de administrador.
    5. Haga clic en **Siguiente**.
6. En el cuadro de diálogo siguiente **Configuración de la máquina virtual**:
    1. En **Servicio en la nube**, use el valor predeterminado: **Crear un nuevo servicio en la nube**.
    2. El valor de **Nombre DNS de servicio** en la nube debe ser exclusivo en cloudapp.net. Si es necesario, modifique este valor para que Azure indique que es exclusivo.
    2. Especifique una región, un grupo de afinidad o una red virtual. En este tutorial, especifique una región como **Oeste de EE. UU**.
    2. En **Cuenta de almacenamiento**, seleccione **Usar una cuenta de almacenamiento generada automáticamente**.
    3. En **Conjunto de disponibilidad**, seleccione **(Ninguno)**.
    4. Haga clic en **Siguiente**.
7. En el cuadro de diálogo final **Configuración de la máquina virtual**:
    1. Acepte las entradas de extremo predeterminadas.
    2. Haga clic en **Completo**.

## Para iniciar sesión de manera remota en la máquina virtual

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Máquinas virtuales**.
3. Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.
4. Una vez que la máquina virtual se haya iniciado, un menú emergente en la parte inferior de la página permite las conexiones.
5. Haga clic en **Conectar**.
6. Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Esto debería implicar el guardado o la apertura del archivo .rdp que contiene los detalles de conexión. Es posible que tenga que copiar el valor de url:port de la parte final de la primera línea del archivo .rdp y pegarlo en una aplicación remota de inicio de sesión.

## Para instalar un servidor de aplicaciones Java en la máquina virtual

Puede copiar un servidor de aplicaciones Java en la máquina virtual o instalarlo a través de un instalador.

En este tutorial, se instalará Tomcat.

1. Cuando haya iniciado sesión en la máquina virtual, abra una sesión del explorador en [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Haga doble clic en el vínculo del **instalador del servicio de Windows de 32 bits y 64 bits**. Mediante esta técnica, Tomcat se instalará como servicio de Windows.
3. Cuando se le pida, elija ejecutar el instalador.
4. En el asistente para la **instalación de Apache Tomcat**, siga las indicaciones para instalar Tomcat. En este tutorial, es adecuado aceptar los valores predeterminados. Cuando llegue al cuadro de diálogo **Completing the Apache Tomcat Setup Wizard** (Finalización del asistente para la instalación de Apache Tomcat), si lo desea, puede activar **Run Apache Tomcat** (Ejecutar Apache Tomcat) para iniciar Tomcat ahora. Haga clic en **Finalizar** para finalizar el proceso de instalación de Tomcat.

## Para iniciar Tomcat
Si no ha elegido ejecutar Tomcat en el cuadro de diálogo **Completing the Apache Tomcat Setup Wizard** (Finalización del asistente para la instalación de Apache Tomcat), inícielo; para ello, abra un símbolo del sistema en la máquina virtual y ejecute **net start Tomcat7**.

Ahora, si ejecuta el explorador de la máquina virtual y abre <http://localhost:8080>., verá que Tomcat se está ejecutando.

Para ver que Tomcat se ejecuta desde máquinas externas, deberá crear un extremo y abrir un puerto.

## Para crear un extremo para la máquina virtual
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Máquinas virtuales**.
3. Haga clic en el nombre de la máquina virtual que ejecuta el servidor de aplicaciones Java.
4. Haga clic en **Extremos**.
5. Haga clic en **Agregar**.
6. En el cuadro de diálogo **Agregar extremo**, asegúrese de que la casilla **Agregar extremo independiente** está seleccionada y después haga clic en **Siguiente**.
7. En el cuadro de diálogo **Detalles del nuevo extremo**:
    1. Especifique un nombre para el extremo; por ejemplo, **HttpIn**.
    2. Especifique **TCP** para el protocolo.
    3. Especifique **80** para el puerto público.
    4. Especifique **8080** para el puerto privado.
    5. Haga clic en el botón **Completar** para cerrar el cuadro de diálogo. Ahora se creará el extremo.

## Para abrir un puerto en el firewall para la máquina virtual
1. Inicie sesión en la máquina virtual.
2. Haga clic en **Inicio de Windows**.
3. Haga clic en **Panel de control**.
4. Haga clic en **Sistema y seguridad**, **Firewall de Windows** y, a continuación, en **Configuración avanzada**.
5. Haga clic en **Reglas de entrada** y después en **Nueva regla**. ![Nueva regla de entrada][NewIBRule]
6. En **Tipo de regla**, seleccione **Puerto** y haga clic en **Siguiente**. ![Puerto de nueva regla de entrada][NewRulePort]
7. En la pantalla **Protocolo y puertos**, seleccione **TCP**, especifique **8080** como **Puerto local específico** y, a continuación, haga clic en **Siguiente**. ![Nueva regla de entrada][NewRuleProtocol]
8. En la pantalla **Acción**, seleccione **Permitir la conexión** y haga clic en **Siguiente**. ![Acción de nueva regla de entrada][NewRuleAction]
9. En la pantalla **Perfil**, asegúrese de que **Dominio**, **Privado** y **Público** estén activados y después haga clic en **Siguiente**. ![Perfil de nueva regla de entrada][NewRuleProfile]
10. En la pantalla **Nombre**, especifique un nombre para la regla, por ejemplo **HttpIn** (sin embargo, no es necesario que el nombre de la regla coincida con el nombre del extremo) y haga clic en **Finalizar**. ![Nombre de la nueva regla de entrada][NewRuleName]

En este momento, el sitio web de Tomcat se debería ver desde un explorador externo, usando una URL con el formato **http://*your\_DNS\_name*.cloudapp.net**, donde ***su\_nombre\_DNS*** es el nombre DNS que especificó cuando creó la máquina virtual.

## Consideraciones acerca del ciclo de vida de las aplicaciones
* Puede crear su propio archivo de aplicación web (WAR) y agregarlo a la carpeta **webapps**. Por ejemplo, cree un proyecto web dinámico JSP (Java Service Page) básico y expórtelo como un archivo WAR; copie el archivo WAR en la carpeta **webapps** de Apache Tomcat en la máquina virtual y, a continuación, ejecútelo en un explorador.
* De forma predeterminada, cuando el servicio Tomcat está instalado, está configurado para iniciarse manualmente. Puede cambiarlo para que se inicie automáticamente mediante el complemento Servicios. Inicie el complemento Servicios, haga clic en **iniciar Windows**, **Herramientas administrativas** y, a continuación, **Servicios**. Haga doble clic en el servicio **Apache Tomcat** y establezca **Tipo de inicio** en **Automático**.

    ![Configurar un servicio para que se inicie automáticamente][service_automatic_startup]

    La ventaja de que Tomcat se inicie automáticamente es que se iniciará si se inicia la máquina virtual (por ejemplo, después de instalar actualizaciones de software que requieren un reinicio).

## Pasos siguientes
Para obtener más información sobre otros servicios (como Almacenamiento de Azure, bus de servicio y Base de datos SQL) que desee incluir con sus aplicaciones Java, consulte la información disponible en el [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png

<!---HONumber=AcomDC_0128_2016-->