A continuación, si algún servidor del clúster está ejecutando Windows Server 2008 R2 o Windows Server 2012, tienes que comprobar que la revisión [KB2854082](http://support.microsoft.com/kb/2854082) está instalada en todos los servidores locales o máquinas virtuales (VM) de Azure que forman parte del clúster. Cualquier servidor o VM que esté en el clúster, pero no en el grupo de disponibilidad, también debería tener instalada esta revisión.

En la sesión de escritorio remoto para todos los nodos del clúster, descarga la [KB2854082](http://support.microsoft.com/kb/2854082) en un directorio local. Después, instala la revisión en todos los nodos del clúster de forma secuencial. Si en ese momento el servicio de clúster se está ejecutando en el nodo de clúster, el servidor se reiniciará al final de la instalación de la revisión.

>[AZURE.WARNING]Detener el servicio de clúster o reiniciar el servidor afectará al estado del cuórum del clúster y al grupo de disponibilidad y puede provocar que el clúster se quede sin conexión. Para mantener la alta disponibilidad del clúster durante la instalación, asegúrate de que:
>
> - El clúster se encuentra en un estado de cuórum óptimo 
> - Todos los nodos del clúster están conectados antes de instalar la revisión en cualquier nodo
> - Se permite que se ejecute la instalación de la revisión hasta que finalice en un nodo, incluido el reinicio completo del servidor, antes de instalar la revisión en ningún otro nodo del clúster.

<!-------HONumber=Oct15_HO3-->