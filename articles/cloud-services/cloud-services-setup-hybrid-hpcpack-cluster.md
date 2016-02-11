<properties
	pageTitle="Configurar un clúster HPC híbrido con Microsoft HPC Pack | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Microsoft HPC Pack y Azure para configurar un pequeño clúster híbrido de informática de alto rendimiento (HPC)"
	services="cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management,hpc-pack"/>

<tags
	ms.service="cloud-services"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="danlep"/>


# Configurar un clúster de proceso híbrido con Microsoft HPC Pack e instancias de Azure bajo petición
Este tutorial le indica cómo usar Microsoft HPC Pack 2012 R2 y Azure para configurar un pequeño clúster híbrido de informática de alto rendimiento (HPC). El clúster constará de un nodo principal local (un equipo con el sistema operativo Windows Server y HPC Pack) y otros nodos de ejecución que implemente localmente como instancias de rol de trabajo en un servicio en la nube de Azure. A continuación, podrá ejecutar trabajos informáticos en el clúster híbrido.

![Clúster híbrido de HPC][Overview]

Este tutorial muestra un enfoque, en ocasiones denominado clúster "ráfaga en la nube", para usar recursos informáticos escalables y bajo demanda en Azure a fin de ejecutar aplicaciones informáticas que consumen numerosos recursos.

En este tutorial se supone que no cuenta con experiencia previa con los clústeres informáticos o con HPC Pack. Únicamente está destinado a ayudarle a implementar un clúster de proceso híbrido rápidamente con fines de demostración. Para obtener más información, así como pasos para implementar un clúster híbrido HPC Pack a mayor escala en un entorno de producción, consulte la [información detallada](http://go.microsoft.com/fwlink/p/?LinkID=200493). Para otros escenarios de HPC Pack, incluida la implementación automatizada de clústeres en máquinas virtuales de Azure, consulte [Opciones de clúster HPC con Microsoft HPC Pack en Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md).

>[AZURE.NOTE] Azure ofrece una [serie de tamaños](../virtual-machines/virtual-machines-size-specs.md) para los recursos informáticos, adecuados para las diferentes cargas de trabajo. Por ejemplo, las instancias A8 y A9 combinan alto rendimiento y acceso a baja latencia, aunque se necesita una red de aplicaciones de alto rendimiento para determinadas aplicaciones de HPC. Consulte [Sobre las instancias informáticas intensivas A8, A9, A10 y A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

## Requisitos previos

* **Suscripción a Azure:** si no tiene ninguna cuenta, puede crear una cuenta gratuita de evaluación en un par de minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un equipo local que ejecute Windows Server 2012 R2 o Windows Server 2012**. Este equipo será el nodo principal del clúster de HPC. Si todavía no tiene Windows Server, puede descargar e instalar una [versión de evaluación](http://technet.microsoft.com/evalcenter/dn205286.aspx).

	* El equipo debe estar unido a un dominio de Active Directory.

	* Asegúrese de que no haya otros roles de servidor o servicios de rol adicionales instalados.

	* Para admitir HPC Pack, el sistema operativo debe estar instalado en uno de estos idiomas: inglés, japonés o chino (simplificado).

	* Compruebe que estén instaladas las actualizaciones importantes y esenciales.

* **HPC Pack 2012 R2** - [Descargue](http://go.microsoft.com/fwlink/p/?linkid=328024) el paquete de instalación completo de la última versión gratis y copie los archivos en el equipo del nodo principal o en una ubicación de red. Elija los archivos de instalación que tengan el mismo idioma que su instalación de Windows Server.

* **Cuenta de dominio**: esta cuenta se debe configurar con permisos de administrador local en el nodo principal para instalar HPC Pack.

* **Conectividad TCP en el puerto 443** desde el nodo principal a Azure.

## Instalación de HPC Pack en el nodo principal

En primer lugar, debe instalar Microsoft HPC Pack en un equipo local con un Windows Server que será el nodo principal del clúster.

1. Inicie sesión en el nodo principal usando una cuenta de dominio que tenga permisos de administrador local.

2. Inicie el asistente de instalación de HPC Pack ejecutando el archivo Setup.exe desde los archivos de instalación de HPC Pack.

3. En la pantalla de **instalación de HPC Pack 2012 R2**, haga clic en **Nueva instalación o agregar características a una instalación existente**.

	![Instalación de HPC Pack 2012][install_hpc1]

4. En la página **Contrato de usuario de software de Microsoft**, haga clic en **Siguiente**.

5. En la página **Seleccionar tipo de instalación**, haga clic en **Crear un clúster de HPC creando un nodo principal** y, a continuación, haga clic en **Siguiente**.

	![Selección del tipo de instalación][install_hpc2]

6. El asistente ejecuta varias pruebas de preinstalación. Haga clic en **Next** en la página **Installation Rules** si se pasan todas las pruebas. Si no, revise la información proporcionada y realice los cambios necesarios en su entorno. A continuación, ejecute las pruebas de nuevo o, si fuera necesario, vuelva a iniciar el asistente de instalación.

	![Reglas de instalación][install_hpc3]

7. En la página **Configuración de la base de datos de HPC**, asegúrese de haber seleccionado **Nodo principal** en todas las bases de datos de HPC y, a continuación, haga clic en **Siguiente**.

	![Configuración de la base de datos][install_hpc4]

8. Acepte las opciones seleccionadas de forma predeterminada en las páginas restantes del asistente. En la página **Instalar componentes** requeridos, haga clic en **Instalar**.

	![Instalación][install_hpc6]

9. Cuando finalice la instalación, desactive **Iniciar Administrador de clústeres de HPC** y, a continuación, haga clic en **Finalizar**. (Se iniciará el Administrador de clústeres HPC en un paso posterior).

	![Finalización][install_hpc7]

## Preparación de la suscripción de Azure
Use el [Portal de Azure clásico](https://manage.windowsazure.com) para realizar los siguientes pasos con su suscripción de Azure. Estos pasos son necesarios para poder implementar más adelante los nodos de Azure desde el nodo principal local:

- Cargue un certificado de administración (necesario para las conexiones seguras entre el nodo principal y los servicios de Azure).

- Cree un servicio en la nube de Azure en el que se ejecutarán los nodos de Azure (instancias de rol de trabajo).

- Creación de una cuenta de almacenamiento de Azure

	>[AZURE.NOTE]Anote también el Id. de la suscripción de Azure, ya que lo necesitará más adelante. Podrá encontrarlo en la [información de la cuenta](https://account.windowsazure.com/Subscriptions)</a> de Azure.

### Cargar el certificado de administración predeterminado
HPC Pack instala un certificado autofirmado en el nodo principal, llamado Default Microsoft HPC Azure Management, que podrá cargar como certificado de administración de Azure. Este certificado se proporciona con fines de prueba e implementaciones de prueba de concepto.

1. En el equipo del nodo principal, inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

2. Haga clic en **Configuración** y, a continuación, en **Certificados de administración**.

3. En la barra de comandos, haga clic en **Cargar**.

	![Configuración del certificado][upload_cert1]

4. Busque el archivo C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer en el nodo principal. A continuación, haga clic en el botón **Comprobar**.

	![Carga del certificado][install_hpc10]

Aparecerá **Administración de HPC de Azure predeterminado** en la lista de certificados de administración.

### Crear un servicio en la nube de Azure

>[AZURE.NOTE]Para conseguir un mejor rendimiento, cree el servicio en la nube y la cuenta de almacenamiento en la misma región geográfica.

1. En la barra de comandos del portal clásico, haga clic en **Nuevo**.

2. Haga clic en **Proceso**, en **Servicio en la nube** y, a continuación, **en Creación rápida**.

3. Escriba una dirección URL para el servicio en la nube y, a continuación, haga clic en **Crear servicio en la nube**.

	![Creación de un servicio][createservice1]

### Creación de una cuenta de almacenamiento de Azure

1. En la barra de comandos del portal clásico, haga clic en **Nuevo**.

2. Haga clic en **Servicios de datos**, en **Almacenamiento** y, a continuación, en **Creación rápida**.

3. Escriba una dirección URL para la cuenta y, a continuación, haga clic en **Crear cuenta de almacenamiento**.

	![Creación de almacenamiento][createstorage1]

## Configuración del nodo principal

Para usar el administrador de clústeres de HPC para implementar nodos de Azure y enviar trabajos, primero debe seguir los pasos necesarios para configurar el clúster.

1. En el nodo principal, inicie el administrador de clústeres de HPC. Si aparece el cuadro de diálogo **Select Head Node**, haga clic en **Local Computer**. Aparecerá la **lista de tareas pendientes de implementación**.

2. En **Tareas de implementación necesarias**, haga clic en **Configurar la red**.

	![Configuración de la red][config_hpc2]

3. En el Asistente para configuración de red, seleccione **Todos los nodos solo en una red empresarial** (topología 5).

	![Topología 5][config_hpc3]

	>[AZURE.NOTE]Esta es la configuración más sencilla para fines de demostración, ya que el nodo principal solo necesita un solo adaptador de red para conectarse a Active Directory e Internet. Este tutorial no trata las situaciones de clúster que requieren redes adicionales.

4. Haga clic en **Siguiente** para aceptar los valores predeterminados de las páginas restantes del asistente. A continuación, en la pestaña **Revisar**, haga clic en **Configurar** para completar la configuración de red.

5. En la **lista de tareas pendientes de implementación**, haga clic en **Proporcionar credenciales de instalación**.

6. En el cuadro de diálogo **Credenciales de instalación**, escriba las credenciales de la cuenta de dominio que ha usado para instalar HPC Pack. y, a continuación, haga clic en **Aceptar**.

	![Credenciales de instalación][config_hpc6]

	>[AZURE.NOTE]Los servicios de HPC Pack solo usan credenciales de instalación para implementar nodos de ejecución unidos a un dominio. Los nodos de Azure que agrega en este tutorial no están unidos a un dominio.

7. En la **lista de tareas pendientes de implementación**, haga clic en **Configurar la nomenclatura de los nuevos nodos**.

8. En el cuadro de diálogo **Especificar serie de nomenclatura de nodos**, acepte la serie de nomenclatura predeterminada y haga clic en **Aceptar**.

	![Nomenclatura de nodos][config_hpc8]

	>[AZURE.NOTE]La serie de nomenclatura solo genera nombres para nodos de ejecución unidos a un dominio. Los nodos de trabajo de Azure reciben un nombre de forma automática.

9. En la **lista de tareas pendientes de implementación**, haga clic en **Crear una plantilla de nodo**. Usará una plantilla de nodo para agregar nodos de Azure al clúster.

10. En el asistente de creación de plantillas de nodo, siga estos pasos:

	a. En la página **Elegir el tipo de plantilla de nodo**, haga clic en la **plantilla de nodo de Azure** y después haga clic en **Siguiente**.

	![Plantilla de nodo][config_hpc10]

	b. Haga clic en **Siguiente** para aceptar el nombre de plantilla predeterminado.

	c. En la página **Proporcionar información de suscripción**, especifique su identificador de suscripción de Azure (disponible en la información de cuenta de Azure). A continuación, en **Certificado de administración**, haga clic en **Examinar** y seleccione **Administración de HPC de Azure predeterminado.** A continuación, haga clic en **Siguiente**.

	![Plantilla de nodo][config_hpc12]

	d. En la pagina **Proporcionar información del servicio**, seleccione el servicio en la nube y la cuenta de almacenamiento que ha creado en el paso anterior. A continuación, haga clic en **Siguiente**.

	![Plantilla de nodo][config_hpc13]

	e. Haga clic en **Siguiente** para aceptar los valores predeterminados de las páginas restantes del asistente. A continuación, en la pestaña **Revisar**, haga clic en **Crear** para crear la plantilla de nodo.

	>[AZURE.NOTE]De forma predeterminada, la plantilla de nodo de Azure incluye la configuración para que pueda iniciar (aprovisionar) y detener los nodos manualmente. También puede configurar una programación para iniciar y detener los nodos de Azure automáticamente.

## Incorporación de nodos de Azure al clúster

Ahora usará la plantilla de nodo para agregar nodos de Azure al clúster. Al agregar los nodos al clúster, su información de configuración se almacena para que pueda iniciarlos (aprovisionarlos) en cualquier momento como instancias de rol en el servicio en la nube. Su suscripción solo se carga para los nodos de Azure después de que las instancias de rol se ejecuten en el servicio en la nube.

En este tutorial, agregará dos nodos pequeños.

1. En el administrador de clústeres de HPC, en **Administrador de nodos**, en el panel **Acciones**, haga clic en **Agregar nodo**.

	![Agregar un nodo][add_node1]

2. En el asistente para agregar nodos, en la página **Seleccionar método de implementación**, haga clic en **Agregar nodos de Azure** y, a continuación, en **Siguiente**.

	![Agregar un nodo de Azure][add_node1_1]

3. En la página **Especificar nodos nuevos**, seleccione la plantilla de nodo de Azure que creó anteriormente (llamada de manera predeterminada **Default AzureNode Template**). A continuación, especifique **dos** nodos de tamaño **pequeño** y, a continuación, haga clic en **Siguiente**.

	![Especificar los nodos][add_node2]

	Para más información acerca de los tamaños disponibles, consulte [Tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md).

4. En la página **Finalización del asistente para agregar nodos**, haga clic en **Finalizar**.

	 Ahora aparecerán dos nodos llamados **AzureCN-0001** y **AzureCN-0002** en el administrador de clústeres de HPC. Ambos tienen el estado **No implementado**.

	![Nodos agregados][add_node3]

## Inicio de los nodos de Azure
Cuando quiera usar los recursos del clúster de Azure, use el administrador de clústeres de HPC para iniciar (aprovisionar) los nodos de Azure y conectarlos a la red.

1.	En el Administrador de clústeres de HPC, en **Administrador de nodos**, (llamado **Administrador de recursos** en algunas versiones de HPC Pack), haga clic en uno o en ambos nodos y, a continuación, en el panel **Acciones**, haga clic en **Inicio**.

	![Iniciar los nodos][add_node4]

2. En el cuadro de diálogo **Iniciar nodos de Azure**, haga clic en **Iniciar**.

	![Iniciar los nodos][add_node5]

	Los nodos pasan al estado **Aprovisionamiento**. Consulte el registro de aprovisionamiento para realizar el seguimiento del progreso de aprovisionamiento.

	![Aprovisionar nodos][add_node6]

3. Después de unos minutos, los nodos de Azure terminan de aprovisionarse y pasan al estado **Sin conexión**. Las instancias de rol se ejecutan en este estado, pero no aceptan los trabajos de clúster.

4. Para confirmar que las instancias de rol se estén ejecutando, en el [portal clásico](https://manage.windowsazure.com), haga clic en **Servicios en la nube**, después en el nombre de su servicio en la nube y, a continuación, en **Instancias**.

	![Instancias en ejecución][view_instances1]

	Verá que hay dos instancias de rol de trabajo ejecutándose en el servicio. HPC Pack también implementa automáticamente dos instancias **HpcProxy** (tamaño medio) para controlar la comunicación entre el nodo principal y Azure.

5. Para conectar los nodos de Azure y que ejecuten los trabajos de clúster, seleccione los nodos, haga clic con el botón derecho y, a continuación, haga clic en **Poner en línea**.

	![Nodos desconectados][add_node7]

	El administrador de clústeres de HPC indica que los nodos están en estado **En línea**.

## Ejecución de un comando en el clúster

Para comprobar la instalación, use el comando **clusrun** de HPC Pack para ejecutar un comando o una aplicación en uno o varios nodos del clúster. Por ejemplo, use **clusrun** para obtener la configuración IP de los nodos de Azure.

1. En el nodo principal, abra un símbolo del sistema.

2. Escriba el siguiente comando:

	`clusrun /nodes:azurecn* ipconfig`

3. Verá un resultado similar al siguiente.

	![Clusrun][clusrun1]

## Ejecución de un trabajo de prueba

Ahora, envíe un trabajo de prueba que se ejecute en el clúster híbrido. Este ejemplo es un sencillo trabajo de "barrido paramétrico" (un tipo de computación intrínsecamente paralelo) que ejecuta subtareas que se agregan un entero a sí mismas usando el comando **set /a**. Todos los nodos del clúster contribuyen a finalizar las subtareas para enteros del 1 al 100.

1. En el administrador de clústeres de HPC, en **Administración de trabajos**, en el panel **Acciones**, haga clic en **Nuevo trabajo de barrido paramétrico**.

	![Nuevo trabajo][test_job1]

2. En el cuadro de diálogo **Nuevo trabajo de barrido paramétrico**, en la **línea de comandos**, escriba `set /a *+*` (sobrescribiendo la línea de comandos predeterminada que aparezca). Deje los valores predeterminados para el resto de la configuración y, a continuación, haga clic en **Enviar** para enviar el trabajo.

	![Barrido paramétrico][param_sweep1]

3. Cuando termine el trabajo, haga doble clic en el trabajo **Mi tarea de barrido**.

4. Haga clic en **Ver tareas** y, a continuación, haga clic en una subtarea para ver la salida calculada de dicha subtarea.

	![Resultados de la tarea][view_job361]

5. Para ver qué nodo ha realizado el cálculo en esa subtarea, haga clic en **Nodos asignados**. (Su clúster podría mostrar un nombre de nodo diferente).

	![Resultados de la tarea][view_job362]

## Detención de los nodos de Azure

Después de probar el clúster, detenga los nodos de Azure e impida que se produzcan cargas innecesarias en la cuenta. De este modo se detiene el servicio en la nube y se eliminan las instancias de rol de Azure.

1. En el administrador de clústeres de HPC, en **Administración de nodos**, seleccione ambos nodos de Azure. A continuación, en el panel **Acciones**, haga clic en **Detener**.

	![Detener los nodos][stop_node1]

2. En el cuadro de diálogo **Detener nodos de Azure**, haga clic en **Detener**.

	![Detener los nodos][stop_node2]

3. Los nodos pasan al estado **En detención**. Después de unos minutos, el administrador de clústeres de HPC indica que los nodos tienen el estado **No implementado**.

	![Nodos no implementados][stop_node4]

4. Para confirmar que las instancias de rol han dejado de ejecutarse en Azure, en el [portal](https://manage.windowsazure.com), haga clic en **Servicios en la nube**, después en el nombre de su servicio en la nube y, a continuación, en **Instancias**. No se implementarán instancias en el entorno de producción.

	![Sin instancias][view_instances2]

	Con esto finaliza el tutorial.

## Recursos relacionados

* [HPC Pack 2012 R2 y HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697

* [Irrumpir en instancias de rol de trabajo de Azure con HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Opciones de clúster de HPC con Microsoft HPC Pack en Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)
* [Big Compute en Azure: recursos técnicos para Batch e informática de alto rendimiento (HPC)](../batch/big-compute-resources.md)


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png

<!---HONumber=AcomDC_0128_2016-->