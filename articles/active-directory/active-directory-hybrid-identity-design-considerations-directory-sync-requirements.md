<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: determinación de los requisitos de sincronización de directorios| Microsoft Azure"
	description="Identifique qué requisitos son necesarios para sincronizar todos los usuarios entre el entorno local y la nube en la empresa."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="billmath"/>

# Determinación de los requisitos de sincronización de directorios
La sincronización consiste en proporcionar a los usuarios una identidad en la nube en función de su identidad local. Tanto si usan una cuenta sincronizada para la autenticación como la autenticación federada, los usuarios deben tener una identidad en la nube. Esta identidad se debe mantener y actualizar periódicamente. Las actualizaciones pueden adoptar muchas formas, desde cambios en los títulos a cambios de contraseña.

Para comenzar, evalúe la solución de identidad local de la organización y los requisitos de los usuarios. Esta evaluación es importante para definir los requisitos técnicos respecto a cómo se crean y mantienen las identidades de los usuarios en la nube. En la mayoría de las organizaciones, Active Directory es local y estará en el directorio local desde el que se sincronizarán los usuarios; sin embargo, otras veces, no será este el caso.

Asegúrese de responder a las siguientes preguntas:


- ¿Tiene un bosque de AD único, múltiple o no tiene ninguno?
 - ¿Con cuántos directorios de Azure AD va a sincronizar?
 
    1. ¿Usará filtrado?
    2. ¿Tiene varios servidores de Azure AD Connect planeados?
  
- ¿Dispone actualmente de una herramienta de sincronización local?
  - En caso afirmativo, ¿tienen sus usuarios una directorio virtual o integración de identidades?
- ¿Tiene algún otro directorio local que quiera sincronizar (por ejemplo, un directorio LDAP, una base de datos HR, etc.)?
  - ¿Va a realizar alguna operación GALSync?
  - ¿Cuál es el estado actual de los UPN de su organización? 
  - ¿Tiene un directorio diferente en el que se autentican los usuarios?
  - ¿Usa su organización Microsoft Exchange?
    - ¿Tienen previsto tener una implementación híbrida de Exchange? 
   
Ahora que tiene una idea sobre los requisitos de sincronización de su empresa, debe evaluar las aplicaciones que usan estos servicios de directorio. Esta evaluación es importante para definir los requisitos técnicos para integrar estas aplicaciones en la nube. Asegúrese de responder a las siguientes preguntas:

- ¿Se migrarán estas aplicaciones a la nube y usarán el directorio?
- ¿Hay atributos especiales que sea necesario sincronizar con la nube para que estas aplicaciones puedan usarlos correctamente?
- ¿Será necesario volver a escribir estas aplicaciones para aprovechar la autenticación en la nube?
- ¿Seguirán estas aplicaciones activas de forma local mientras los usuarios acceden a ellas mediante la identidad de nube?

También deberá determinar los requisitos de seguridad y las restricciones de la sincronización de directorios. Esta evaluación es importante para obtener una lista de los requisitos que serán necesarios para crear y mantener las identidades del usuario en la nube. Asegúrese de responder a las siguientes preguntas:

- ¿Dónde estará ubicado el servidor de sincronización?
- ¿Estará unido a un dominio?
- ¿Estará ubicado el servidor en una red restringida detrás de un firewall, por ejemplo, una red perimetral (DMZ)?
  - ¿Podrá abrir los puertos de firewall necesarias para admitir la sincronización?
- ¿Tiene un plan de recuperación ante desastres para el servidor de sincronización?
- ¿Tiene una cuenta con los permisos correctos para todos los bosques con lo que quiera sincronizar?
 - Si su empresa no conoce la respuesta a esta pregunta, revise la sección "Permisos para la sincronización de contraseñas" en el artículo [Instalación del servicio de sincronización de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) y determine si ya tiene una cuenta con estos permisos o si es necesario crear una.
- Si tiene sincronización de varios bosques, ¿podrá llegar el servidor de sincronización a cada bosque?
 
>[AZURE.NOTE]Asegúrese de anotar cada respuesta y de que comprende las razones que se esconden detrás. En la sección [Determinación de los requisitos de respuesta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) recorrerá las opciones disponibles. Las respuestas que obtenga partir de estas preguntas le servirán para seleccionar la opción que mejor se adapte a sus necesidades empresariales.

## Pasos siguientes
[Determinación de los requisitos de autenticación multifactor](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## Consulte también
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->