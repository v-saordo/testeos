<properties
	pageTitle="Preguntas más frecuentes de Azure AD Connect Health"
	description="Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad."
	services="active-directory"
	documentationCenter=""
	authors="karavar"
	manager="samueld"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="vakarand"/>


# Preguntas más frecuentes sobre Azure AD Connect Health

Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad.

## Preguntas generales



**P: Administro varios directorios de Azure AD. ¿Cómo puedo cambiar al inquilino que tiene Azure Active Directory Premium?**

Puede alternar entre los distintos directorios de Azure AD seleccionando el nombre de usuario con el que se ha iniciado sesión actualmente en la esquina superior derecha y eligiendo la cuenta adecuada. Si la cuenta no aparece aquí, seleccione Cerrar sesión y luego use las credenciales de administrador global del directorio que tiene Azure Active Directory Premium habilitado para iniciar sesión.

## Preguntas sobre la instalación



**P: ¿Qué impacto tiene la instalación del agente de Azure AD Connect Health en los servidores individuales?**

El impacto de la instalación del agente de Microsoft Identity Health en los servidores de ADFS es mínimo con respecto a la CPU, al consumo de memoria, al ancho de banda y al almacenamiento.

Los números siguientes son una aproximación.

- Consumo de CPU: aumento de ~1%
- Consumo de memoria: hasta 10 % de la memoria total del sistema
- Uso de ancho de banda de la red: ~1 MB por cada 1000 solicitudes de ADFS

>[AZURE.NOTE] En caso de que el agente no se pueda comunicar con Azure, el agente almacenará los datos localmente, hasta un límite máximo definido. Cuando el agente alcance el límite, si el agente no ha podido cargar los datos en el servicio, las nuevas transacciones de ADFS sobrescribirán cualquier transacción "en caché" como "menos atendida recientemente".

- Almacenamiento en búfer local para el agente de AD Health: ~20 MB
- Almacenamiento de datos requerido para canal de auditoría


Se recomienda que aprovisione un espacio en disco de 1024 MB (1 GB) para que el canal de auditoría de AD FS para agentes de AD Health procese todos los datos.

**P: ¿Tendré que reiniciar los servidores durante la instalación de los agentes de Azure AD Connect Health?**

No. La instalación de los agentes no requerirá que reinicie el servidor. Sin embargo, la instalación de algunos de los requisitos previos podría requerir reiniciar el servidor.

En Windows Server 2008 R2, por ejemplo, la instalación de .Net 4.5 Framework requiere un reinicio del servidor.


**P: ¿Los Servicios de Azure AD Connect Health funcionan mediante un proxy HTTP de paso a través?**

Sí. Para las operaciones en curso, puede configurar el agente de mantenimiento para reenviar las solicitudes http de salida con un proxy HTTP. Consulte [Configuración de agentes de Azure AD Connect Health para usar el proxy HTTP](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy) para obtener más información..

Si necesita configurar a un proxy durante el registro del agente, debe modificar su configuración del proxy de Internet Explorer. <br> Abra Internet Explorer -> Configuración -> Opciones de Internet -> Conexiones -> Configuración de LAN.<br> Seleccione Usar un servidor proxy para la LAN.<br> Seleccione Opciones avanzadas si tiene distintos puertos de proxy para HTTP y HTTPS/Secure.<br>


**P: ¿Servicios de Azure AD Connect Health admiten la autenticación básica cuando se conectan con servidores proxy de HTTP?**

No. Actualmente, no se admite un mecanismo para especificar un nombre de usuario/contraseña arbitrarios para la autenticación básica.



## Preguntas sobre las operaciones



**P: ¿Es necesario habilitar la auditoría en los servidores proxy de aplicación de AD FS o de aplicación web?**

No. No es necesario habilitar la auditoría en los servidores proxy de aplicación de AD FS o de aplicación web. Solo es necesario habilitarla en los servidores federados de AD FS.



**P: ¿Cómo se resuelven las alertas de Azure AD Connect Health?**

Las alertas de Azure AD Connect Health se resuelven con una condición de acierto. Los agentes de Azure AD Connect Health detectan e informan las condiciones de acierto al servicio de manera periódica. En el caso de algunas alertas, la supresión depende del tiempo. Es decir, si la misma condición de error no se observa dentro de 48 horas desde la generación de la alerta, esta se resuelve de forma automática.




**P: ¿Qué puertos de firewall debo abrir para que funcione el agente de Azure AD Connect Health?**

Deberá abrir los puertos 80, 443 y 5671 TCP/UDP para que el agente de Azure AD Connect Health pueda comunicarse con los extremos de servicio de Azure AD Health.

## Vínculos relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)

<!---HONumber=AcomDC_0128_2016-->