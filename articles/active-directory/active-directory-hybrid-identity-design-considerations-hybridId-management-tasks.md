<properties
	pageTitle="Consideraciones sobre el diseño de identidad híbrida de Azure Active Directory: determinación de las tareas de administración de identidad híbrida| Microsoft Azure"
	description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."
	documentationCenter=""
	services="active-directory"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/10/2016"
	ms.author="femila"/>

# Plan para el ciclo de vida de identidad híbrida 

La identidad es una de las bases de la estrategia de acceso a las aplicaciones y movilidad de la empresa. Si va a iniciar sesión en su dispositivo móvil o aplicación SaaS, su identidad es la clave para obtener acceso a todo. En su nivel más alto, una solución de administración de identidades abarca la unificación y la sincronización entre los repositorios de identidad, lo que incluye la automatización y la centralización del proceso de aprovisionamiento de recursos. La solución de identidad debe ser una identidad centralizada entre el entorno local y la nube y también usar algún tipo de federación de identidades para mantener la autenticación centralizada y compartir y colaborar de forma segura con usuarios y empresas externos. Los recursos van desde sistemas operativos y aplicaciones hasta personas de una organización o afiliadas a ella. Se puede modificar la estructura organizativa para dar cabida a las directivas y procedimientos de aprovisionamiento.

También es importante contar con una solución de identidad preparada para dar atribuciones a los usuarios de forma que les proporcione experiencias de autoservicio que les mantenga productivos. La solución de identidad es más eficaz si habilita el inicio de sesión único para los usuarios en todos los recursos a los que necesitan tener acceso. Los administradores de todos los niveles pueden usar procedimientos estandarizados para administrar las credenciales de usuario. Algunos niveles de administración se pueden reducir o eliminar, según la amplitud de la solución de administración de aprovisionamiento. Además, se pueden distribuir de manera segura funcionalidades de administración, manual o automáticamente, entre varias organizaciones. Por ejemplo, un administrador de dominio puede atender solo a las personas y los recursos de ese dominio. Este usuario puede realizar tareas administrativas y de aprovisionamiento, pero no está autorizado a realizar tareas de configuración, como la creación de flujos de trabajo.


## Determinación de las tareas de administración de identidad híbrida
La distribución de las tareas administrativas en una organización mejora la precisión y la efectividad de la administración y el equilibrio de la carga de trabajo. Los siguientes son los ejes que definen un sistema sólido de administración de identidades.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Para definir las tareas de administración de identidad híbrida, debe entender algunas características esenciales de la organización que va a adoptar dicha identidad. Es importante comprender los repositorios actuales que se usan para los orígenes de identidades. Si conoce esos elementos básicos, tendrá los requisitos fundamentales y, en función de eso, deberá realizar preguntas más precisas que le lleven a una mejor decisión de diseño de su solución de identidad.

Al definir esos requisitos, asegúrese de que al menos puede contestar a las siguientes preguntas.

- Opciones de aprovisionamiento: 
 - ¿Admite la solución de identidad híbrida un sistema sólido de aprovisionamiento y administración de acceso a las cuentas?
 - ¿Cómo se administrarán los usuarios, los grupos y las contraseñas?
 - ¿Responde adecuadamente la administración del ciclo de vida de las identidades? 
      - ¿Cuánto tiempo tarda la suspensión de la cuenta de actualización de contraseña?
      
- Administración de licencias:
 - ¿Se ocupa la solución de identidad híbrida de la administración de licencias?
     - En caso afirmativo, ¿qué funcionalidades están disponibles?
- ¿Controla la solución la administración de licencias basada en grupos? 
      - En caso afirmativo, ¿se le puede asignar un grupo de seguridad? 
       - En caso afirmativo, ¿asignará el directorio en la nube licencias a todos los miembros del grupo automáticamente? 
        - En caso de que un usuario se agregue posteriormente al grupo o se elimina de este, ¿se asignará una licencia automáticamente o se eliminará según corresponda? 

- Integración con otros proveedores de identidades de terceros:
- ¿Se puede integrar esta solución híbrida con proveedores de identidades de terceros para implementar el inicio de sesión único?
- ¿Es posible unificar los distintos proveedores de identidades en un sistema de identidad coherente?
- En caso afirmativo, ¿cómo y cuáles son y que funcionalidades hay disponibles?

## Administración de la sincronización
Uno de los objetivos de un administrador de identidades es poder reunir todos los proveedores de identidades y mantenerlos sincronizados. Los datos se mantienen sincronizados en función de un proveedor de identidades maestro autorizado. En un escenario de identidad híbrida, con un modelo de administración sincronizada, todas las identidades de los usuarios y de los dispositivos se administran en un servidor local y la cuentas y, opcionalmente las contraseñas, se sincronizan con la nube. El usuario escribe la misma contraseña en el entorno local y en la nube y, en el inicio de sesión, la solución de identidad la comprueba. En este modelo se emplea una herramienta de sincronización de directorios.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png) Para diseñar correctamente la sincronización de la solución de identidad híbrida, asegúrese de que tiene una respuesta a las siguientes preguntas: • ¿Cuáles son las soluciones de sincronización disponibles para la solución de identidad híbrida? • ¿Qué funcionalidades de inicio de sesión único hay disponibles? • ¿Cuáles son las opciones para la federación de identidades entre B2B y B2C?

## Pasos siguientes
[Determinación de la estrategia de adopción de administración de identidad híbrida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## Otras referencias
[Información general sobre las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0218_2016-->