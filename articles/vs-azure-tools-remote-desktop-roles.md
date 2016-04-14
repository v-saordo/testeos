<properties 
   pageTitle="Uso de Escritorio remoto con roles de Azure | Microsoft Azure"
   description="Uso de Escritorio de remoto con los roles de Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="tarcher" />

# Uso de Escritorio de remoto con los roles de Azure

Mediante el SDK de Azure y los servicios de escritorio remoto, puede acceder a los roles de Azure y las máquinas virtuales que se hospedan en Azure. En Visual Studio, puede configurar el servicio de Escritorio remoto desde un proyecto de Azure. Para habilitar los servicios de Escritorio remoto, tiene que crear un proyecto que contenga uno o más roles y, a continuación, publicarlo en Azure.

>[AZURE.IMPORTANT]Debe tener acceso a un rol de Azure para solucionar problemas o desarrollo solamente. El propósito de cada máquina virtual es ejecutar una función específica en su aplicación de Azure, no es ejecutar otras aplicaciones cliente. Si desea usar Azure para hospedar una máquina virtual que se puede usar para cualquier propósito, consulte Tener acceso a Máquinas virtuales de Azure desde el Explorador de servidores.

## Para habilitar y usar el Escritorio remoto para un rol de Azure

1. En el Explorador de soluciones, abra el menú contextual de su proyecto y, a continuación, elija **Publicar**.

    Aparecerá el **Asistente para publicar aplicación de Azure**.

    ![Publicar el comando para un proyecto de servicio en la nube](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. En la parte inferior de página del asistente **Configuración de publicación de Microsoft Azure**, seleccione la casilla **Habilitar Escritorio remoto** de todos los roles.

    Aparece el cuadro de diálogo **Configuración de Escritorio remoto**.

1. En la parte inferior del cuadro de diálogo **Configuración de escritorio remoto** , seleccione el botón **Más opciones**.
 
    Esto muestra un cuadro de lista desplegable que le permite crear o seleccionar un certificado para que puede cifrar la información de credenciales al conectarse a través de escritorio remoto.

1. En la lista desplegable, elija **&lt;Crear>**, o elija uno existente en la lista.

    Si elige un certificado existente, omita los pasos siguientes.

    >[AZURE.NOTE]Los certificados que necesita para una conexión de escritorio remoto son distintos de los certificados que se usan para otras operaciones de Azure. El certificado de acceso remoto tiene que tener una clave privada.

    Aparece el cuadro de diálogo **Crear certificado**.

    1. Proporcione un nombre descriptivo para el nuevo certificado y luego seleccione el botón **Aceptar**. El nuevo certificado aparece en el cuadro de lista desplegable.

    1. En el cuadro de diálogo **Configuración de escritorio remoto**, proporcione un nombre de usuario y una contraseña.
    
        No se puede usar una cuenta existente. No especifique Administrador como el nombre de usuario de la nueva cuenta.

        >[AZURE.NOTE]Si la contraseña no cumple los requisitos de complejidad, aparecerá un icono rojo junto al cuadro de texto de contraseña. La contraseña debe incluir letras mayúsculas, letras minúsculas y números o símbolos.

    1. Elija una fecha en la que caducará la cuenta y pasada la cual se bloquearán las conexiones a Escritorio remoto.

    1. Una vez que haya dado toda la información necesaria, elija el botón **Aceptar**.
    
        Se agregan varias opciones a los archivos .cscfg y .csdef. que activan los servicios de acceso remoto.

1. En el asistente de **Configuración de publicación de Microsoft Azure** , elija el botón **Aceptar** cuando esté listo para publicar su servicio en la nube.

    Si no está preparado para publicar, elija el botón **Cancelar**. Se guardan los valores de configuración, y puede publicar su servicio en la nube más adelante.

## Conexión a un rol de Azure mediante Escritorio remoto

Después de publicar el servicio en la nube en Azure, puede usar el Explorador de servidores para iniciar sesión en las máquinas virtuales que hospeda Azure.

1. En el Explorador de servidores, expanda el nodo **Azure** y luego expanda el nodo para un servicio en la nube y uno de sus roles para mostrar una lista de instancias.

1. Abra el menú contextual de un nodo de instancia y luego elija **Conectar con Escritorio remoto**.

    ![Conexión a través del escritorio remoto](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Escriba el nombre de usuario y la contraseña que ha creado anteriormente. Ahora está registrado en la sesión remota.

<!---HONumber=AcomDC_1223_2015-->