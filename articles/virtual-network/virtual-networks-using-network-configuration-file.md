<properties 
	pageTitle="Configuración de una red virtual con un archivo de configuración de red" 
	description="Instrucciones para exportar e importar un archivo de configuración de red en el Portal de administración de Azure para crear o modificar redes virtuales." 
	services="virtual-network" 
	documentationCenter="" 
	authors="telmosampaio" 
	manager="carmonm" 
	editor="tysonn"/>

<tags
	ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services" 
	ms.date="12/07/2015"
	ms.author="telmos"/>

# Configuración de una red virtual con un archivo de configuración de red

Puede configurar una red virtual (VNet) mediante el Portal de administración de Azure o mediante un archivo de configuración de red.

## Creación y modificación de un archivo de configuración de red 
La manera más fácil de crear un archivo de configuración de red es exportar la configuración de red desde una configuración de red virtual existente, modificar el archivo para que contenga los ajustes con los que desea configurar las redes virtuales.

Para editar el archivo de configuración de red, puede simplemente abrir el archivo, realizar los cambios adecuados y, después, guardarlo. Puede usar cualquier editor *xml* para realizar cambios en el archivo de configuración de red.

Se deben seguir meticulosamente las instrucciones sobre los [parámetros del esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Azure considera una subred que tiene algo implementado en ella como **en uso**. Cuando una subred está en uso, no puede modificarse. Antes de la modificación, mueva todo lo que haya implementado en la subred a una subred diferente que no se esté modificando. Consulte [Mover una máquina virtual o instancia de rol a una subred diferente](virtual-networks-move-vm-role-to-subnet.md).

## Exportación e importación de la configuración de red virtual mediante el Portal de administración  
Puede importar y exportar las opciones de configuración de una red contenidas en el archivo de configuración de red mediante PowerShell o el Portal de administración. Las instrucciones siguientes le ayudarán a exportar e importar con el Portal de administración.

### Para exportar la configuración de red
Al exportar, todos los ajustes de las redes virtuales en su suscripción se escribirán en un archivo .xml.

1. Inicie sesión en el **Portal de administración**.
2. En el Portal de administración, en la parte inferior de la página **Redes**, haga clic en **Exportar**. 
3. En la ventana **Exportar configuración de red**, compruebe que seleccionó la suscripción cuya configuración de red desee exportar. A continuación, haga clic en la marca de verificación de la esquina inferior derecha. 
4. Cuando se le solicite, guarde el archivo *NetworkConfig.xml* en la ubicación que prefiera.


### Para importar la configuración de red

1. En el **Portal de administración**, en el panel de navegación de la parte inferior izquierda, haga clic en **Nuevo**.
2. Haga clic en **Servicios de red** -> **Red virtual** -> **Importar configuración**.
3. En la página **Importar el archivo de configuración de red**, busque el archivo de configuración de red y, a continuación, haga clic en la flecha **Siguiente**.
4. En la página **Creando su red**, verá información en pantalla que muestra las secciones de la configuración de red que cambiarán o se crearán. Si está de acuerdo con los cambios, haga clic en la marca de verificación para continuar con la actualización o la creación de la red virtual. 

<!---HONumber=AcomDC_1210_2015-->