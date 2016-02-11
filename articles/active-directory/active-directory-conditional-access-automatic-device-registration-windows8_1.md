<properties
	pageTitle="Configure el registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio| Microsoft Azure"
	description="Pasos para configurar la Directiva de grupo a fin de registrar automáticamente dispositivos Windows 8.1 unidos a un dominio en Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>

# Configuración del registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio

Puede usar una Directiva de grupo de Active Directory para configurar sus dispositivos Windows 8.1 unidos a un dominio y registrarse automáticamente en Azure AD. Para configurar la Directiva de grupo, debe tener al menos un equipo con Windows Server 2012 R2 o Windows 8.1 unido a un dominio con la característica Administración de directivas de grupo instalada. Una vez habilitada esta Directiva de grupo para su dominio, cualquier usuario del dominio que inicie sesión en el equipo se registrará de forma automática y silenciosa en un objeto de dispositivo en Azure AD. Habrá un objeto de dispositivo en Azure AD para todos los usuarios registrados del dispositivo físico. Asegúrese de leer y completar los requisitos previos del registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio.

## Configure la Directiva de grupo para sus dispositivos Windows 8.1 unidos a un dominio

1. Abra el Administrador del servidor y vaya a **Herramientas** > **Administración de directivas de grupo**.
2. En Administración de directivas de grupo, vaya al nodo de dominio que corresponde al dominio en el que desea habilitar la **unión automática al área de trabajo**.
3. Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**. Asigne un nombre a su objeto de directiva de grupo, por ejemplo, **Unión automática al área de trabajo**. Haga clic en **OK**.
4. Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y, a continuación, seleccione **Editar**.
5. Vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Unión al área de trabajo**.
6. Haga clic con el botón derecho en Unir automáticamente equipos cliente al área de trabajo y, a continuación, seleccione **Editar**.
7. Seleccione el botón de opción Habilitado y, a continuación, haga clic en Aplicar. Haga clic en **Aceptar**.
8. Ahora puede vincular el objeto de directiva de grupo a una ubicación de su elección. Para habilitar esta directiva para todos los dispositivos Windows 8.1 unidos a un dominio en su organización, vincule la Directiva de grupo al dominio.

## Anulación del registro de sus dispositivos Windows 8.1 unidos a un dominio

Puede elegir anular el registro de sus dispositivos Windows 8.1 unidos a un dominio haciendo lo siguiente: modifique la configuración de la Directiva de grupo de Unión al área de trabajo creada en la sección anterior. Establezca la directiva Unir automáticamente equipos cliente al área de trabajo en deshabilitada. Esto impedirá la unión automática de nuevos dispositivos al área de trabajo.

Anule el registro de los equipos Windows 8.1 unidos a un dominio existentes siguiendo una de las dos opciones siguientes:

* Opción 1: **anule el registro de un dispositivo Windows 8.1 unido a un dominio con Configuración de PC **
  1. En el dispositivo Windows 8.1, vaya a **Configuración de PC** > **Red** > **Área de trabajo**
  2. Seleccione **Abandonar**. Este proceso debe repetirse para cada usuario del dominio que ha iniciado sesión en el equipo y se ha unido automáticamente al área de trabajo.

* Opción 2: anule el registro de un dispositivo Windows 8.1 unido a un dominio con un script
  	1. Abra un símbolo del sistema en el equipo con Windows 8.1 y ejecute el siguiente comando: ` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Este comando debe ejecutarse en el contexto de cada usuario del dominio que ha iniciado sesión en el equipo. Visor de eventos y errores para dispositivos Windows 8.1 unidos a un dominio

El registro de eventos de Windows en el equipo con Windows 8.1 mostrará mensajes relacionados con el registro de dispositivos. Encontrará mensajes para eventos con éxito y sin éxito. El registro de eventos puede encontrarse en el Visor de eventos en **Registros** de aplicaciones y servicios > **Microsoft** > **Windows > Unión al área de trabajo**.

##Detalles adicionales

La Directiva de grupo habilita una tarea programada en el sistema que se ejecuta en el contexto del usuario y se desencadena en el inicio de sesión de usuario. La tarea registra silenciosamente el usuario y dispositivo en Azure AD después de completarse el inicio de sesión. La tarea programada se puede encontrar en dispositivos Windows 8.1 en la Biblioteca del Programador de tareas en **Microsoft** > **Windows** > **Unión al área de trabajo**. La tarea ejecutará y registrará todos los usuarios de Active Directory que inicien sesión.

## Otros temas
- [Información general sobre el Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)
- [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio](active-directory-conditional-access-automatic-device-registration.md)
- [Configure el registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)

<!---HONumber=AcomDC_1203_2015-->