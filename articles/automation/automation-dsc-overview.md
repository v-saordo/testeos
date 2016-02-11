<properties 
   pageTitle="Información general de DSC de Automatización de Azure | Microsoft Azure" 
   description="Una información general de Configuración de estado deseado (DSC) de Automatización de Azure, sus condiciones y problemas conocidos" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="01/22/2016"
   ms.author="coreyp"/>

# Información general de DSC de Automatización de Azure #

##¿Qué es DSC de Automatización de Azure?##
Implementar y mantener el estado deseado de los servidores y de los recursos de aplicación pueden ser tareas laboriosas y propensas a error. Con la Configuración de estado deseado (DSC) de Automatización de Azure, puede implementar de forma coherente, supervisar de forma confiable y actualizar automáticamente el estado deseado de todos los recursos de TI a escala desde la nube. Basado en DSC de PowerShell, DSC de Automatización puede alinear la configuración de máquina con un estado específico de las máquinas físicas y virtuales (VM), con Windows o Linux, y en la nube o de forma local. Puede permitir la entrega continua de servicios de TI gracias al control coherente y administrar con facilidad los cambios rápidos en su entorno heterogéneo de TI híbrida.

DSC de Automatización de Azure se basa en los elementos fundamentales presentados en DSC de PowerShell para proporcionar una experiencia de administración de configuración incluso más simple. DSC de Automatización de Azure lleva a [Configuración de estado deseado de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) la misma capa de administración que ofrece en la actualidad Automatización de Azure para la creación de scripts de PowerShell.

DSC de Automatización de Azure le permite [crear y administrar Configuraciones de estado deseado de PowerShell](https://technet.microsoft.com/library/dn249918.aspx), importar [Recursos de DSC](https://technet.microsoft.com/library/dn282125.aspx) y generar Configuraciones de nodo de DSC (documentos MOF), todo en la nube. Estos elementos de DSC se colocarán en el [servidor de extracción de DSC](https://technet.microsoft.com/library/dn249913.aspx) de Automatización de Azure, para que los nodos de destino (como las máquinas físicas y virtuales) en la nube o locales puedan tomarlos, cumplir automáticamente con el estado deseado especificado e informar según su cumplimiento con el estado deseado a Automatización de Azure.

¿Prefiere ver a leer? Eche un vistazo al vídeo siguiente, de mayo de 2015, cuando se anunció DSC de Automatización de Azure. **Nota:** aunque los conceptos y ciclo de vida que se tratan en este vídeo son correctos, DSC de Automatización de Azure ha progresado mucho desde que se grabó en este vídeo. Ahora está disponible con carácter general, tiene una interfaz de usuario mucho más amplia en el Portal de Azure y admite muchas funciones adicionales.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]



## Condiciones de DSC de Automatización de Azure ##
### Configuración ###
DSC de PowerShell introdujo un concepto nuevo llamado configuraciones. Las configuraciones le permiten definir, a través de la sintaxis de PowerShell, el estado deseado de su entorno. Para usar DSC para configurar su entorno, primero defina un bloque de scripts de Windows PowerShell usando la palabra clave Configuration, seguida de un identificador y llaves ({}) para delimitar el bloque.

![texto alternativo](./media/automation-dsc-overview/AADSC_1.png)

Dentro del bloque de configuración puede definir bloques de configuración de nodo que especifican la configuración deseada de un conjunto de nodos (equipos) en su entorno que se deben configurar exactamente de la misma manera. De esta manera, una configuración de nodo representa un "rol" que uno o más nodos deben asumir. Un bloque de configuración de nodo comienza con la palabra clave Node. Esta palabra clave va seguida del nombre del rol, que puede ser una variable o una expresión. Después del nombre del rol, use llaves {} para delimitar el bloque de configuración del nodo.

![texto alternativo](./media/automation-dsc-overview/AADSC_2.png)
 
Dentro del bloque de configuración del nodo, puede definir bloques de recursos para configurar recursos de DSC específicos. Un bloque de recursos comienza con el nombre del recurso, seguido del identificador que desea especificar para ese bloque y, a continuación, llaves {} para delimitar el bloque.

![texto alternativo](./media/automation-dsc-overview/AADSC_3.png)

Para obtener información más detallada acerca de la palabra clave Configuration, consulte: [Understanding Configuration Keyword in Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Descripción de la palabra clave Configuration en Configuración de estado deseado") (Descripción de la palabra clave Configuration en Configuración de estado deseado)

Ejecutar (compilar) una configuración de DSC generará una o más configuraciones de nodo de DSC (documentos MOF), que son aplicados por los nodos de DSC para cumplir con el estado deseado.

DSC de Automatización de Azure le permite importar, crear y compilar configuraciones de DSC en Automatización de Azure, de manera similar a cómo se pueden importar, crear e iniciar runbooks en Automatización de Azure.

>[AZURE.IMPORTANT] Una configuración solo debe contener un bloque de configuración, con el mismo nombre de la configuración, en DSC de Automatización de Azure.


###Configuración de nodo###

Cuando se compila una configuración de DSC, se generan una o más configuraciones de nodo, dependiendo de los bloques de nodo en la configuración. Una configuración de nodo es lo mismo que un "MOF" o "documento de configuración" (si está familiarizado con esos términos de DSC de PS) y representa un "rol", como servidor web o de trabajo, cuyo estado deseado deben asumir uno o más nodos o se debe comprobar que lo cumplen. Los nombres de las configuraciones de nodo en DSC de Automatización de Azure adoptan la forma de "Configuration Name.NodeConfigurationBlockName".

Los nodos de DSC de PS descubren las configuraciones de nodo que deben aplicar a través de métodos de inserción o extracción de DSC. DSC de Automatización de Azure se basa en el método de extracción de DSC, donde los nodos solicitan configuraciones de nodo que deben aplicar desde el servidor de extracción de DSC de Automatización de Azure. Debido a que los nodos realizan la solicitud a DSC de Automatización de Azure, pueden encontrarse detrás de firewalls, tener cerrados todos los puertos de entrada, etc. Solo necesitan acceso saliente a Internet (directamente o a través de un servidor proxy).


###Nodo###

Un nodo de DSC es cualquier equipo que tenga la configuración administrada por DSC. Podría ser una máquina virtual de Azure de Windows o Linux, una máquina virtual local o un host físico, o una máquina de otra nube pública. Los nodos aplican las configuraciones de nodo para cumplir con el estado deseado que definen y mantenerlo, o también pueden informar a un servidor de informes sobre su estado de configuración y cumplimiento frente al estado deseado.

DSC de Automatización de Azure facilita la incorporación de nodos para su administración, y permite cambiar la configuración de nodo asignada al lado servidor de cada nodo, de modo que la próxima vez que un nodo busque en el servidor instrucciones, asumirá un rol distinto y cambiará cómo está configurado y el estado de cumplimiento que debe notificar para que coincida.


###Recurso###
Los recursos de DSC son bloques de creación que puede usar para definir una configuración de Configuración de estado deseado (DSC) de Windows PowerShell. DSC incluye un conjunto de recursos integrados, por ejemplo, para archivos y carpetas, características y roles de servidor, configuración del Registro, variables de entorno y servicios y procesos. Para obtener información acerca de la lista completa de recursos integrados de DSC y cómo usarlos, consulte [Built-In Windows PowerShell Desired State Configuration Resources](https://technet.microsoft.com/library/dn249921.aspx) (Recursos integrados de Configuración de estado deseado de Windows PowerShell).

Los recursos de DSC también se pueden importar como parte de los módulos PowerShell para extender el conjunto de recursos integrados de DSC. Los nodos de DSC extraerán recursos no predeterminados desde el servidor de extracción de DSC, si una configuración de nodo que el nodo debe aplicar contiene referencias a esos recursos. Para obtener información sobre cómo crear recursos personalizados, consulte [Build Custom Windows PowerShell Desired State Configuration Resources](https://technet.microsoft.com/library/dn249927.aspx) (Creación de recursos personalizados de Configuración de estado deseado de Windows PowerShell).

DSC de Automatización de Azure incluye los mismos recursos integrados de DSC que DSC de PS. Es posible agregar recursos adicionales a DSC de Automatización de Azure mediante la importación a Automatización de Azure de los módulos PowerShell que contienen los recursos.


###Trabajo de compilación###
Un trabajo de compilación en DSC de Automatización de Azure es una instancia de compilación de una configuración, para crear una o más configuraciones de nodo. Son similares a los trabajos de runbook de Automatización de Azure, excepto en que no realizan realmente ninguna tarea, salvo la creación de configuraciones de nodo. Toda configuración creada por un trabajo de compilación se coloca automáticamente en el servidor de extracción de DSC de Automatización de Azure y sobrescriben las versiones anteriores de configuraciones de nodo, si existían para esta configuración. El nombre de una configuración de nodo producida por un trabajo de compilación adopta la forma de "ConfigurationName.NodeConfigurationBlockName". Por ejemplo, al compilar la siguiente configuración se generará una configuración de nodo único llamada "MyConfiguration.webserver".


![texto alternativo](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE] Las configuraciones se pueden publicar, al igual que los runbooks. Esto no está relacionado con la colocación de elementos de DSC en el servidor de extracción de DSC de Automatización de Azure. Los trabajos de compilación hacen que se coloquen los elementos de DSC en el servidor de extracción de DSC de Automatización de Azure. Para obtener más información acerca de cómo "publicar" en Automatización de Azure, consulte [Publicar un runbook](https://msdn.microsoft.com/library/dn903765.aspx).


##Ciclo de vida de DSC de Automatización de Azure##
Ir desde una cuenta de automatización vacía a un conjunto administrado de nodos configurados correctamente implica un conjunto de procesos para definir las configuraciones, convertir esas configuraciones en configuraciones de nodo e incorporar nodos a DSC de Automatización de Azure y a las configuraciones de nodo. El siguiente diagrama ilustra el ciclo de vida de DSC de Automatización de Azure:

![texto alternativo](./media/automation-dsc-overview/DSCLifecycle.png)


La siguiente imagen ilustra el proceso paso a paso detallado en el ciclo de vida de DSC. Incluye diferentes formas en que una configuración se importa y aplica a los nodos en Automatización de Azure, los componentes necesarios para que un equipo local admita DSC y las interacciones entre los distintos componentes.

![Arquitectura del DSC](./media/automation-dsc-overview/dsc-architecture.png)

##Problemas conocidos:##

- Al actualizar a WMF 5 RTM, si la máquina ya está registrada como nodo en DSC de Automatización de Azure, anule su registro y vuelva a registrarla después de la actualización de WMF 5 RTM.

- En este momento, DSC de Automatización de Azure no es compatible con configuraciones parciales o compuestas de DSC. Sin embargo, los recursos compuestos de DSC se pueden importar y utilizar exactamente igual que en PowerShell local, lo que permite la reutilización de la configuración.

- La versión más reciente de WMF 5 debe estar instalada para el agente DSC de PowerShell para que Windows pueda comunicarse con Automatización de Azure. La versión más reciente de agente DSC de PowerShell para Linux debe estar instalada para que sea posible la comunicación con el servicio Automatización de Azure.

- El servidor de extracción tradicional de DSC de PowerShell espera que los archivos ZIP del módulo se coloquen en el servidor de extracción con el formato **NombreMódulo\_versión.zip**. Automatización de Azure espera que los módulos PowerShell se importen con nombres con formato **NombreMódulo.zip**. Consulte [esta entrada de blog](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) para obtener más información sobre el formato de módulo de integración que se necesita para importar el módulo a Automatización de Azure.

- Los módulos PowerShell importados a Automatización de Azure no pueden contener archivos .doc o .docx. Algunos módulos PowerShell que contienen recursos de DSC contienen estos archivos, para propósitos de ayuda. Estos archivos se deben quitar de los módulos antes de la importación a Automatización de Azure.

- Cuando un nodo se registra por primera vez con una cuenta de Automatización de Azure o cuando el nodo se cambia para asignarlo a una configuración de nodo distinta de lado servidor, su estado estará en cumplimiento, incluso si el estado del nodo no está realmente en cumplimiento con la configuración de nodo a la que ahora está asignado. Una vez que el nodo realiza su primera extracción y envía su primer informe después del registro o un cambio de asignación de la configuración de nodo, el estado del nodo puede ser de confianza.

- Al incorporar una máquina virtual Windows de Azure para la administración por DSC de Automatización de Azure mediante cualquiera de nuestros métodos de incorporación directa, puede que la máquina virtual tarde hasta una hora en mostrarse como un nodo de DSC en Automatización de Azure. Esto se debe a la instalación de Windows Management Framework 5.0 en la máquina virtual por la extensión de DSC de la máquina virtual de Azure, que es necesario para incorporar la máquina a DSC de Automatización de Azure.

- Tras el registro, cada nodo negocia automáticamente un certificado único para la autenticación que expira después de un año. En la actualidad, el protocolo de registro DSC de PowerShell no puede renovar automáticamente los certificados que vayan a expirar, por lo que tendrá que volver a registrar los nodos transcurrido un año. Antes de volver a registrar, asegúrese de que cada nodo ejecute Windows Management Framework 5.0 RTM. Si el certificado de autenticación de un nodo expira y el nodo no se registra de nuevo, este no podrá comunicarse con la Automatización de Azure y se marcará como "No responde". La actualización del registro se realiza de la misma forma que se registró el nodo inicialmente. Si dicha actualización se realiza en un plazo de 90 días o menos desde la fecha de expiración del certificado o en cualquier momento después de dicha fecha, se generará y usará un certificado nuevo.

- Al actualizar a WMF 5 RTM, si la máquina ya está registrada como nodo en DSC de Automatización de Azure, anule su registro y vuelva a registrarla después de la actualización de WMF 5 RTM. Antes de volver a registrarla, elimine el archivo $env:windir\\system32\\configuration\\DSCEngineCache.mof.

##Artículos relacionados##

- [Incorporación de máquinas para administrarlas con DSC de Automatización de Azure](../automation/automation-dsc-onboarding.md)
- [Compilación de configuraciones en DSC de Automatización de Azure](../automation/automation-dsc-compile.md)
- [Cmdlets de DSC de Automatización de Azure](https://msdn.microsoft.com/library/mt244122.aspx)
- [Precios de DSC de Automatización de Azure](https://azure.microsoft.com/pricing/details/automation/)
- [Implementación continua en las máquinas virtuales de IaaS mediante DSC de Automatización de Azure y Chocolatey](automation-dsc-cd-chocolatey.md)

<!---HONumber=AcomDC_0128_2016-->