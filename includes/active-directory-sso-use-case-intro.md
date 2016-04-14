Las organizaciones usan más aplicaciones de Software como servicio (SaaS) para aumentar la productividad porque la tecnología de nube y herramientas están cada vez más disponibles. A medida que aumenta el número de aplicaciones de SaaS, se convierte en un desafío para los administradores administrar cuentas y derechos de acceso y para los usuarios recordar las distintas contraseñas. La administración de esas aplicaciones individualmente crea más trabajo y es menos seguro.

- Los empleados que tienen que realizar un seguimiento de las contraseñas tienden a usar métodos menos seguros para recordarlas, ya sea escribiendo las contraseñas o usando las mismas contraseñas en varias cuentas.

- Cuando llega un nuevo empleado o se marcha uno, todas sus cuentas deben ser aprovisionadas o desaprovisionadas individualmente.

- Además, los empleados pueden comenzar a usar aplicaciones de SaaS para su trabajo sin pasar por TI, lo que significa que están creando sus propias cuentas en sistemas que los administradores de TI no aprobaron ni supervisaron.

Es una solución para todos estos desafíos es el inicio de sesión único (SSO). Es la manera más sencilla de administrar varias aplicaciones y proporcionar a los usuarios una experiencia coherente de inicio de sesión. Azure Active Directory (Azure AD) proporciona una sólida solución de SSO y dispone de muchas aplicaciones disponibles preintegradas, con tutoriales para que los administradores configuren rápidamente una nueva aplicación y iniciar el aprovisionamiento de usuarios.


## ¿Cómo integra Azure Active Directory las aplicaciones?  

Azure AD le permite integrar sus aplicaciones y cuentas aprovisionadas. Esto puede hacerse a través de uno de estos dos enfoques.

- Si la aplicación está preintegrada en la galería de aplicaciones, puede ir a ese portal para instalar las aplicaciones y configurar los ajustes para permitir el SSO. Para cualquier aplicación de la galería, puede comenzar por seguir las sencillas instrucciones paso a paso presentadas en la galería de aplicaciones y en el Portal de Azure para habilitar el inicio de sesión único.

- Si la aplicación no está en la galería, todavía puede configurar la mayoría de las aplicaciones de Azure AD como una aplicación personalizada. Esto requiere un poco más experiencia técnica para configurarlo. Puede agregar cualquier aplicación que admita SAML 2.0 como aplicación federada o cualquier aplicación que tenga una página de inicio de sesión basada en HTML para usar SSO con contraseña.

En el caso de aquellos usuarios que crearon sus propias cuentas para las aplicaciones SaaS sin estar administradas por TI, la herramienta [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) ofrece una solución. Esta herramienta supervisa el tráfico web para identificar qué aplicaciones se usan en toda la organización y el número de personas que usa cada una de ellas. TI puede usar esta información para saber qué aplicaciones prefieren los usuarios y decidir cuál se va a integrar en Azure AD para SSO.

Al integrar una aplicación en Azure AD, puede asignar las identidades de aplicaciones establecidas de los usuarios a sus respectivas identidades de Azure AD.

<!---HONumber=Oct15_HO3-->