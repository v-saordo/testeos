<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="Aprenda cómo Azure AD Identity Protection permite limitar la capacidad de un atacante para aprovechar una identidad o un dispositivo en peligro y asegurar una identidad o un dispositivo que antes fue sospechoso o que se sabe que estuvo en peligro."
	services="active-directory"
	keywords="azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection es un servicio de seguridad que proporciona una vista consolidada de eventos de riesgo y puntos vulnerables que afectan a las identidades de su organización. Microsoft ha estado protegiendo identidades basadas en la nube durante más de una década, y con Azure AD Identity Protection, Microsoft proporciona estos mismos sistemas de protección a los clientes de empresa. Identity Protection aprovecha las funciones de detección de anomalías existentes de Azure AD (disponibles mediante informes de actividad anómala de Azure AD) e introduce nuevos tipos de eventos de riesgo que pueden detectar anomalías en tiempo real.

> [AZURE.NOTE] Actualmente, la versión preliminar de Identity Protection solo está disponible para los inquilinos de Azure AD que se aprovisionan en Estados Unidos.
 

La mayoría de las infracciones de seguridad tienen lugar cuando los atacantes obtienen acceso a un entorno mediante el robo de identidad de un usuario. Los atacantes son cada vez más eficaces al aprovechar las brechas de otros fabricantes y el uso de ataques de suplantación de identidad sofisticados. Una vez que un atacante obtiene acceso aunque sea a una cuenta de usuario con pocos privilegios, es relativamente sencillo para obtener acceso a importantes recursos de la empresa mediante el desplazamiento lateral. Por lo tanto, es esencial para proteger todas las identidades y, cuando una identidad está en peligro, evitar de forma proactiva que se haga mal uso de la identidad que está en peligro.

Descubrir las identidades en peligro no es tarea fácil. Afortunadamente, Identity Protection puede ayudar: Identity Protection utiliza algoritmos de aprendizaje automático adaptables y heurísticos para detectar anomalías e eventos de riesgo que pueden indicar que se ha puesto en peligro una identidad.
 
Con estos datos, Identity Protection genera informes y alertas que permiten investigar estos eventos de riesgo y tomar las acciones de corrección o mitigación adecuadas.
 
Pero Azure Active Directory Identity Protection es más de una herramienta de supervisión e informes. En función de los eventos de riesgo, Identity Protection calcula un nivel de riesgo del usuario para cada usuario, lo que le permite configurar las directivas basadas en riesgos para proteger automáticamente las identidades de su organización. Estas directivas basadas en riesgos, además de otros controles de acceso condicional proporcionados por Azure Active Directory y EMS, pueden bloquear automáticamente u ofrecer acciones de corrección adaptables que incluyen el restablecimiento de contraseñas y el cumplimiento de la autenticación multifactor.

####Exploración de las funciones de Identity Protection 

**Detección de eventos de riesgo y cuentas peligrosas:**

- Detectar 6 tipos de eventos de riesgo mediante el aprendizaje automático y las reglas heurísticas 

- Calcular los niveles de riesgo del usuario

- Proporcionar recomendaciones personalizadas para mejorar la posición de seguridad general al resaltar los puntos vulnerables

<br>

**Investigación de los eventos de riesgo:**

- Enviar notificaciones de los eventos de riesgo

- Investigar los eventos de riesgo con información pertinente y contextual

- Proporcionar los flujos de trabajo básicos para realizar un seguimiento de las investigaciones

- Proporcionar un fácil acceso a las acciones de corrección, como el restablecimiento de contraseñas

<br>

**Directivas de acceso condicional basadas en riesgos:**

- Directiva para mitigar inicios de sesión peligrosos al bloquear inicios de sesión o solicitar desafíos de la autenticación multifactor.

- Directiva para proteger o bloquear cuentas de usuario peligrosas

- Directiva para que los usuarios tengan que registrar usuarios en la autenticación multifactor


## Detección y riesgo

### Eventos de riesgo

Los eventos de riesgo son episodios marcados como sospechosos por Identity Protection, e indican que es posible que se haya puesto en peligro una identidad. Para obtener una lista completa de los eventos de riesgo, consulte [Tipos de eventos de riesgo](#types-of-risk-events). Algunos de estos eventos de riesgo han estado disponibles mediante los informes de actividades anómalas de Azure AD en el Portal de administración de Azure. La tabla siguiente enumera los distintos tipos de evento de riesgo y el correspondiente informe de **actividades anómalas de Azure AD**. Microsoft seguirá invirtiendo en este espacio y planea mejorar continuamente la precisión de la detección de eventos de riesgo existentes y agregar nuevos tipos de evento de riesgo de forma constante.



| Tipo de evento de riesgo de Identity Protection | Informe de actividades anómalas de Azure AD correspondiente |
| :-- | :-- |
| Credenciales con fugas | Usuarios con credenciales perdidas |
| Viaje imposible a ubicaciones inusuales |	Actividad de inicio de sesión irregular |
| Inicios de sesión desde dispositivos infectados | Inicios de sesión desde dispositivos posiblemente infectados |
| Inicios de sesión desde direcciones IP anónimas | Inicios de sesión desde orígenes desconocidos |
| Inicios de sesión desde direcciones IP con actividad sospechosa |	Inicios de sesión desde direcciones IP con actividad sospechosa |
| Inicios de sesión desde ubicaciones desconocidas | - | | Bloqueo de eventos (no en la versión preliminar pública) | - |

Los siguientes informes de actividades anómalas de Azure AD no se incluyen como eventos de riesgo en Azure AD Identity Protection y, por tanto, no estarán disponibles mediante Identity Protection. Estos informes aún están disponibles en el Portal de administración de Azure; sin embargo, dejarán de estar en uso en el futuro, ya que están siendo reemplazados por los eventos de riesgo de Identity Protection.

- Inicios de sesión tras varios errores
- Inicios de sesión desde varias ubicaciones geográficas

### Nivel de riesgo

El nivel de riesgo de un evento de riesgo es una indicación (Alto, Medio o Bajo) de la gravedad del evento de riesgo. El nivel de riesgo ayuda a los usuarios de Identity Protection a dar prioridad a las acciones que deben tomar para reducir el riesgo para su organización. La gravedad del evento de riesgo representa la fuerza de la señal como predicción del riesgo de la identidad, combinada con la cantidad de ruido que proporciona normalmente.

- **Alto**: evento de riesgo de gravedad alta y de alta confianza. Estos eventos son buenos indicadores de que se ha puesto en peligro la identidad del usuario; las cuentas de usuario afectadas deben corregirse inmediatamente.

- **Medio**: evento de riesgo de gravedad alta, pero confianza inferior, o viceversa. Estos eventos son potencialmente peligrosos y las cuentas de usuario afectadas deben corregirse.

- **Bajo**: evento de riesgo de baja confianza y gravedad baja. Este evento puede no requerir una acción inmediata, pero cuando se combina con otros eventos de riesgo, puede proporcionar una indicación clara de que la identidad está en peligro.


![Nivel de riesgo](./media/active-directory-identityprotection/01.png "Nivel de riesgo")

 

Los eventos de riesgo se identifican **en tiempo real**, o bien en el procesamiento posterior a que se haya producido el evento (sin conexión). Actualmente la mayoría de los eventos de riesgo en Identity Protection se calculan sin conexión y aparecen en Identity Protection en un plazo de 2 a 4 horas. Aunque se evalúan en tiempo real, los eventos de riesgo en tiempo real se mostrarán en la consola de Identity Protection en un plazo de 5 a 10 minutos.

Varios clientes heredados no admiten actualmente la detección y prevención de eventos de riesgo en tiempo real. Como resultado, los inicios de sesión desde estos clientes no se puede detectar ni prevenir en tiempo real.

### Tipos de eventos de riesgo

En esta sección se proporciona información general de los tipos de eventos de riesgo disponibles

#### Credenciales con fugas

Los investigadores de seguridad de Microsoft han encontrado credenciales con fugas publicadas en la Web oscura. Estas credenciales se encuentran normalmente en texto sin formato. Se comprueban con credenciales de Azure AD y, si hay una coincidencia, se notifican como "Credenciales con fugas" en Identity Protection.

Los eventos de riesgo de credenciales con fugas se clasifican con gravedad "Alta", ya que proporcionan una indicación clara de que el nombre de usuario y la contraseña están a disposición de un atacante.

#### Viaje imposible a ubicaciones inusuales

Este tipo de evento de riesgo identifica dos inicios de sesión procedentes de ubicaciones geográficamente distantes, donde al menos una de las ubicaciones puede también ser inusual para el usuario, según su comportamiento anterior. Además, el tiempo entre los dos inicios de sesión es menor que el tiempo que habría necesitado el usuario para viajar de la primera ubicación a la segunda, lo que indica que otro usuario utiliza las mismas credenciales.

Este algoritmo de aprendizaje automático omite "*falsos positivos*" obvios que contribuyen a la condición de viaje imposible, como las redes privadas virtuales y ubicaciones usadas con regularidad por otros usuarios de la organización. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual aprende el comportamiento de inicios de sesión del nuevo usuario.

Un viaje imposible suele ser un buen indicador de que un hacker logró iniciar sesión correctamente. Sin embargo, pueden producirse falsos positivos cuando un usuario viaja con un nuevo dispositivo o usa una VPN que normalmente no utilizan otros usuarios de la organización. Otra fuente de falsos positivos son las aplicaciones que pasan incorrectamente direcciones IP del servidor como IP de cliente, lo que puede dar la impresión de que los inicios de sesión tienen lugar desde el centro de datos en el que está hospedado el back-end de esa aplicación (a menudo son centros de datos de Microsoft, por lo que parece que los inicios de sesión tienen lugar en direcciones IP propiedad de Microsoft). Como resultado de estos falsos positivos, el nivel de riesgo de estos eventos de riesgo es "**Medio**".

####Inicios de sesión desde dispositivos infectados

Este tipo de evento de riesgo identifica los inicios de sesión desde dispositivos infectados con malware, que se sabe que se comunican activamente con un servidor bot. Esto se determina mediante la correlación de direcciones IP de dispositivos de usuarios con direcciones IP que estuvieron en contacto con un servidor bot.

Este evento de riesgo identifica las direcciones IP, no los dispositivos de usuarios. Si hay varios dispositivos detrás de una única dirección IP, y solo algunos son controlados por una red bot, los inicios de sesión desde otros dispositivos pueden desencadenar este evento innecesariamente, por lo que se clasifica este evento de riesgo como "**Bajo**".

Se recomienda ponerse en contacto con el usuario y examinar todos sus dispositivos. También es posible que el dispositivo personal de un usuario esté infectado o, como se ha mencionado antes, que otra persona estaba usando un dispositivo infectado desde la misma dirección IP que el usuario. A menudo, los dispositivos infectados son infectados por malware que todavía no ha sido identificado por el software antivirus, y también pueden indicar unos hábitos del usuario incorrectos que pueden haber hecho que el dispositivo se infecte.

Para obtener más información acerca de cómo tratar infecciones de malware, consulte el [Centro de protección contra malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


#### Inicios de sesión desde direcciones IP anónimas

Este tipo de evento de riesgo identifica los usuarios que han iniciado sesión correctamente desde una dirección IP que se ha identificado como una dirección IP de proxy anónima. A menudo, estos servidores proxy los usan los usuarios que desean ocultar la dirección IP del dispositivo y es posible que se usen con fines malintencionados.

Se recomienda ponerse en contacto inmediatamente con el usuario para comprobar si utilizaba direcciones IP anónimas. El nivel de riesgo de este tipo de evento es ""**Medio**" porque, en sí misma, una IP anónima no es una indicación clara de una cuenta en riesgo.

#### Inicios de sesión desde direcciones IP con actividad sospechosa

Este tipo de evento de riesgo identifica las direcciones IP desde las que se ha producido un gran número de intentos fallidos de inicio de sesión, en varias cuentas de usuario, durante un corto período de tiempo. Esto compara los patrones de tráfico de direcciones IP usadas por atacantes y es un claro indicador de que las cuentas están ya o van a estar en peligro. Se trata de un algoritmo de aprendizaje automático que omite "*falsos positivos*" obvios, como las direcciones IP usadas con regularidad por otros usuarios de la organización. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual aprende el comportamiento de inicio de sesión de un nuevo usuario y un nuevo inquilino.

Se recomienda ponerse en contacto con el usuario para comprobar si realmente iniciaron sesión desde una dirección IP marcada como sospechosa. El nivel de riesgo de este tipo de evento es "**Medio**" porque varios dispositivos pueden encontrarse detrás de la misma dirección IP, mientras que solo algunos pueden ser responsables de la actividad sospechosa.


#### Inicio de sesión desde ubicaciones desconocidas

Este tipo de evento de riesgo es un mecanismo de evaluación de inicio de sesión en tiempo real que tiene en cuenta las ubicaciones de inicio de sesión anteriores (IP, latitud/longitud y ASN) para determinar las ubicaciones nuevas o desconocidas. El sistema almacena información acerca de las ubicaciones anteriores utilizadas por un usuario y considera estas ubicaciones "conocidas". El evento de riesgo se desencadena cuando el inicio de sesión se produce desde una ubicación que no está en la lista de ubicaciones conocidas. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual no marca ninguna nueva ubicación como ubicación desconocida. El sistema también ignora los inicios de sesión desde dispositivos conocidos y ubicaciones geográficamente cercanas a una ubicación conocida. <br> Las ubicaciones desconocidas pueden proporcionar una indicación clara de que un atacante puede intentar utilizar una identidad robada. Se pueden producir falsos positivos cuando un usuario está viajando o probando un nuevo dispositivo, o bien utiliza una VPN nueva. Como resultado de estos falsos positivos, el nivel de riesgo para este tipo de evento es "**Medio**".

### Puntos vulnerables

Los puntos vulnerables son puntos débiles de su entorno que pueden ser aprovechados por un atacante. Se recomienda solucionar estos puntos vulnerables para mejorar la posición de seguridad de su organización y evitar que los atacantes aprovechen estos puntos vulnerables.

#### Registro de la autenticación multifactor no configurado 

Este punto vulnerable ayuda a controlar la implementación de Azure Multi-Factor Authentication en su organización.

Azure Multi-Factor Authentication proporciona una segunda capa de seguridad a la autenticación de usuario. Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de opciones sencillas, como llamadas telefónicas, mensajes de texto o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran.

Se recomienda requerir la autenticación multifactor para los inicios de sesión de usuario. La autenticación multifactor desempeña un papel clave en las directivas de acceso condicional basadas en riesgos disponibles mediante Identity Protection.

Para obtener más detalles, consulte [Qué es Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).


#### Aplicaciones en la nube no administradas

Este punto vulnerable ayuda a identificar aplicaciones en la nube no administradas en su organización.
 
En las empresas modernas, los departamentos de TI a menudo no son conscientes de todas las aplicaciones en la nube que usan los miembros de su organización para realizar su trabajo. Es fácil ver por qué a los administradores les podría preocupar el acceso no autorizado a datos corporativos, la posible pérdida de datos y otros riesgos de seguridad.

Le recomendamos que su organización implemente Cloud App Discovery para detectar aplicaciones en la nube no administradas, así como administrar estas aplicaciones con Azure Active Directory.

Para más detalles, consulte [Búsqueda de aplicaciones de nube no administradas con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)



####Alertas de seguridad de administración de identidades con privilegios

Este punto vulnerable ayuda a detectar y resolver alertas acerca de las identidades con privilegios en su organización.

Para que los usuarios puedan realizar operaciones con privilegios, las organizaciones tienen que proporcionar a sus usuarios acceso con privilegios temporal o permanente en Azure AD, recursos de Azure u Office 365, y otras aplicaciones de SaaS. Cada uno de estos usuarios con privilegios aumenta la superficie de ataque de su organización. Este punto vulnerable ayuda a identificar a los usuarios con acceso con privilegios innecesarios y realizar la acción apropiada para reducir o eliminar el riesgo que suponen.

Recomendamos que la organización utilice Privileged Identity Management de Azure AD para administrar, controlar y supervisar las identidades con privilegios y su acceso a recursos en Azure AD y en otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

Para más detalles, consulte [Privileged Identity Management de Azure AD](active-directory-privileged-identity-management-configure.md).

## Investigación
Normalmente su viaje a través de Identity Protection empieza en el panel de Identity Protection.

<br><br> ![Corrección](./media/active-directory-identityprotection/29.png "Corrección") <br>

El panel proporciona acceso a:
 
- Informes, como **Usuarios marcados como de riesgo**, **Eventos de riesgo** y **Puntos vulnerables**.
- Opciones como la configuración de las **Directivas de seguridad**, **Notificaciones** y el **registro de la autenticación multifactor**.
 

Normalmente es el punto de partida para la investigación, que es el proceso de revisión de las actividades, los registros y otra información pertinente relacionada con un evento de riesgo para decidir si son necesarios pasos de mitigación o de corrección, y cómo se ha puesto en peligro la identidad así como comprender cómo se utilizaba la identidad en peligro.


### Notificaciones

Azure AD Identity Protection envía dos tipos de correos electrónicos de notificación automatizados para ayudar a administrar el riesgo del usuario y los eventos de riesgo:

- Correo electrónico de alerta para usuarios en peligro

- Correo electrónico de resumen semanal

#### Correo electrónico de alerta para usuarios en peligro

Se genera una alerta de correo electrónico para usuarios en peligro cuando Azure AD Identity Protection identifica que una cuenta se ha puesto en peligro. El correo electrónico incluye un vínculo al informe Usuarios marcados como de riesgo en la consola de Identity Protection. Se recomienda que las notificaciones de usuarios puestos en peligro se investiguen inmediatamente.


#### Correo electrónico de resumen semanal

El correo electrónico de resumen semanal contiene un resumen de nuevos eventos de riesgo.<br> Incluye:
- Usuarios en riesgo
- Eventos de riesgo notificados
- Puntos vulnerables detectados
- Vínculos a los informes relacionados en Identity Protection


<br> ![Corrección](./media/active-directory-identityprotection/400.png "Corrección") <br>

De forma predeterminada, se envía la alerta de usuario en peligro a todos los administradores de Azure Active Directory. Puede personalizar los destinatarios:

- Mediante el vínculo de administración de destinatarios en la parte inferior de la alerta

- Al hacer clic en Notificaciones en la configuración de Identity Protection
 
<br> ![Riesgos de usuario](./media/active-directory-identityprotection/62.png "Riesgos de usuario") <br>
 



## ¿Qué es un nivel de riesgo de usuario?

El nivel de riesgo de un usuario es una indicación (Alto, Medio o Bajo) de la probabilidad de que se haya puesto en peligro la identidad del usuario. Se calcula en función de los eventos de riesgo del usuario que están asociados a la identidad del usuario.

El estado de un evento de riesgo es **Activo** o **Cerrado**. Solo los eventos de riesgo con el estado **Activo** contribuyen al cálculo del riesgo del usuario.

El nivel de riesgo del usuario se calcula con las siguientes entradas:

- Eventos de riesgo activos que afectan al usuario
- Nivel de riesgo de estos eventos 
- Si se ha tomado alguna acción de corrección 

<br> ![Riesgos de usuario](./media/active-directory-identityprotection/86.png "Riesgos de usuario") <br>



Puede utilizar los niveles de riesgo del usuario para crear directivas de acceso condicionales para impedir que los usuarios peligrosos inicien sesión u obligarles a cambiar su contraseña de forma segura.


## Cierre manual de eventos de riesgo

En la mayoría de los casos, realizará las acciones de corrección, como restablecer una contraseña segura, para cerrar automáticamente los eventos de riesgo. Sin embargo, esto no siempre es posible. <br> Dado que los eventos de riesgo que tienen el estado **Activo** contribuyen al cálculo de riesgo del usuario, puede que tenga que reducir manualmente un nivel de riesgo al cerrar manualmente los eventos de riesgo. <br> Durante el curso de investigación, puede realizar cualquiera de estas acciones para cambiar el estado de un evento de riesgo:

<br> ![Acciones](./media/active-directory-identityprotection/34.png "Acciones") <br>

- **Resolver**: si después de investigar un evento de riesgo, se ha tomado una acción de corrección adecuada fuera de Identity Protection y cree que se debe considerar el evento de riesgo cerrado, marque el evento como resuelto. Los eventos resueltos establecerán el estado del evento de riesgo en Cerrado y ya no contribuirá al riesgo del usuario.

- **Marcar como falso positivo**: en algunos casos, puede investigar un evento de riesgo y detectar que se ha marcado incorrectamente como un peligroso. Para ayudar a reducir el número de dichos casos, puede marcar el evento de riesgo como falso positivo. Esto ayudará a los algoritmos de aprendizaje automático a mejorar la clasificación de eventos similares en el futuro. El estado de los eventos con falsos positivos es **Cerrado** y ya no contribuyen al riesgo del usuario.

- **Omitir**: si no se ha tomado ninguna acción de corrección, pero desea que el evento de riesgo que quite de la lista activa, puede seleccionar Omitir para un evento de riesgo, cuyo estado será Cerrado. Los eventos omitidos no contribuyen al riesgo del usuario. Esta opción solo debe utilizarse en circunstancias inusuales.

- **Reactivar**: los eventos de riesgo que se cierran manualmente (al elegir **Resolver**, **Falso positivo** u **Omitir**) se pueden reactivar al establecer el estado del evento de nuevo en **Activo**. Los eventos de riesgo reactivados contribuyen al cálculo del nivel de riesgo del usuario. Los eventos de riesgo cerrados mediante una corrección (como el restablecimiento de una contraseña segura) no se pueden reactivar.



## Corrección de eventos de riesgo del usuario

Una corrección es una acción para proteger una identidad o un dispositivo que antes fue sospechoso o que se sabe que estuvo en peligro. Una acción de corrección restaura la identidad o el dispositivo a un estado seguro y resuelve los anteriores eventos de riesgo asociados a la identidad o el dispositivo.

Para corregir los eventos de riesgo del usuario, puede:

- Realizar un restablecimiento de contraseña segura para corregir manualmente los eventos de riesgo del usuario. 

- Configurar una directiva de seguridad de riesgo del usuario para mitigar o corregir automáticamente los eventos de riesgo del usuario.

- Recrear la imagen del dispositivo infectado.


### Restablecimiento de una contraseña segura

Restablecer una contraseña segura es una corrección eficaz para muchos de los eventos de riesgo y, cuando se realiza, estos eventos de riesgo cierran y vuelven a calcular automáticamente el nivel de riesgo del usuario. Puede utilizar la consola de Identity Protection para iniciar el restablecimiento de la contraseña de un usuario en peligro.

La consola proporciona dos maneras diferentes para restablecer contraseñas:

**Restablecer contraseña**: seleccione **Solicitar al usuario que restablezca la contraseña** para permitir al usuario una recuperación automática si el usuario se ha registrado para la autenticación multifactor. Durante el siguiente inicio de sesión del usuario, el usuario debe resolver un desafío de autenticación multifactor correctamente y, a continuación, debe cambiar la contraseña. Esta opción no está disponible si la cuenta de usuario aún no tiene registrada la autenticación multifactor.

**Contraseña temporal**: seleccione **Generar una contraseña temporal** para invalidar inmediatamente la contraseña existente y crear una nueva contraseña temporal para el usuario. Envíe la nueva contraseña temporal a una dirección de correo electrónico alternativa para el usuario o el administrador del usuario. Dado que la contraseña es temporal, se le pedirá al usuario que cambie la contraseña al iniciar sesión.

<br> ![Directiva](./media/active-directory-identityprotection/71.png "Directiva") <br>



## Directiva de seguridad de riesgo del usuario

Una directiva de seguridad de riesgo del usuario es una directiva de acceso condicional que evalúa el nivel de riesgo de un usuario específico y aplica acciones de corrección y mitigación según las reglas y condiciones predefinidas. <br><br> ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/500.png "Directiva de riesgo de usuario") <br>

Azure AD Identity Protection ayuda a administrar la mitigación y corrección de usuarios marcados como peligrosos al permitirle:

- Configurar los usuarios y grupos a los que se aplica la directiva. <br><br> ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/501.png "Directiva de riesgo de usuario") <br>

- Establecer el umbral de nivel de riesgo del usuario (bajo, medio o alto) que desencadena un cambio de contraseña. <br><br> ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/502.png "Directiva de riesgo de usuario") <br>

- Establecer el umbral de nivel de riesgo del usuario (bajo, medio o alto) que desencadena el bloqueo de un usuario. <br><br> ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/503.png "Directiva de riesgo de usuario") <br>

- Revisar y evaluar el impacto de un cambio antes de activarlo. <br><br> ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/504.png "Directiva de riesgo de usuario") <br>


Elegir un umbral **Alto** reduce el número de veces que una directiva se desencadena y minimiza el impacto en los usuarios. Sin embargo, excluye los usuarios marcados con un riesgo **Bajo** y **Medio** por la directiva, lo que puede no proteger las identidades o dispositivos que antes fueron sospechosos o que se sabe que estuvieron en peligro.

Al establecer la directiva:

- Excluya a los usuarios que es probable que generen una gran cantidad de falsos positivos (desarrolladores, analistas de seguridad).

- Excluya a los usuarios que tengan configuraciones regionales en las que no es práctico habilitar la directiva (por ejemplo, no tienen acceso al departamento de soporte técnico).

- Utilice un umbral **Alto** durante la puesta en servicio inicial de la directiva, o bien si debe reducir al mínimo los desafíos vistos por los usuarios finales.

- Utilice un umbral **Bajo** si su organización requiere una mayor seguridad. Seleccionar un umbral **Bajo** presenta desafíos adicionales de inicio de sesión del usuario, pero aumenta la seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones es configurar una regla para un umbral **Medio** con el fin de lograr un equilibrio entre la facilidad de uso y la seguridad.



### Mitigación de los eventos de riesgo del usuario
Los administradores pueden establecer una directiva de seguridad de riesgo del usuario para bloquear a los usuarios al iniciar sesión en función del nivel de riesgo.

El bloqueo de un inicio de sesión:
 
- Previene la generación de nuevos eventos de riesgo del usuario para el usuario afectado.

- Permite a los administradores corregir los eventos de riesgo que afectan a la identidad del usuario y restaurarlos a un estado seguro.





### Flujos de corrección y mitigación de riesgos del usuario  

Las secciones siguientes proporcionan información general de la experiencia del usuario para flujos de corrección y mitigación de riesgos del usuario.

#### Flujo de recuperación de cuentas en peligro

Cuando se ha configurado una directiva de seguridad de riesgo del usuario, los usuarios que cumplen el nivel especificado en dicha directiva (y, por tanto, se supone que está en peligro) deben pasar por el flujo de recuperación del peligro antes de poder iniciar sesión.

El flujo de recuperación de usuarios puestos en peligro consta de tres pasos:

1. Se informa al usuario que la seguridad de su cuenta está en peligro debido a actividad sospechosa o credenciales con fugas.

<br> ![Corrección](./media/active-directory-identityprotection/101.png "Corrección") <br>

2.	Para demostrar su identidad, el usuario debe resolver un desafío de seguridad. Si el usuario está registrado para la autenticación multifactor, puede recuperarse automáticamente cuando está en peligro. Necesitará devolver un código de seguridad enviado a su número de teléfono. 

<br> ![Corrección](./media/active-directory-identityprotection/110.png "Corrección") <br>


3.	Por último, el usuario debe cambiar su contraseña, ya que es posible que otra persona haya tenido acceso a su cuenta. A continuación se muestran capturas de pantalla de esta experiencia.
 
<br> ![Corrección](./media/active-directory-identityprotection/111.png "Corrección") <br>







 
#### Restablecimiento de contraseña
Si se bloquea el inicio de sesión de un usuario, un administrador puede generar una contraseña temporal para él. Tendrá que cambiar su contraseña la próxima vez que inicie sesión. Un cambio de contraseña corrige y cierra la mayoría de los tipos de eventos de riesgo para el usuario.

<br> ![Corrección](./media/active-directory-identityprotection/160.png "Corrección") <br>


### Mitigación del nivel de riesgo del usuario

### Cuenta puesta en peligro: bloqueada 

Para desbloquear un usuario bloqueado por una directiva de seguridad de riesgo del usuario, el usuario debe ponerse en contacto con un administrador o el departamento de soporte técnico. La recuperación automática al resolver la autenticación multifactor no es una opción en este caso.

<br> ![Corrección](./media/active-directory-identityprotection/104.png "Corrección") <br>


## Mitigación de eventos de riesgo de inicio de sesión 
Una mitigación es una acción para limitar la capacidad de un atacante de aprovechar una identidad o un dispositivo en peligro sin restaurar la identidad o el dispositivo a un estado seguro. Una mitigación no resuelve los anteriores eventos de riesgo de inicio de sesión asociados a la identidad o el dispositivo.

Puede usar el acceso condicional en Azure AD Identity Protection para mitigar los eventos de riesgo de inicio de sesión automáticamente. Mediante estas directivas, tenga en cuenta el nivel de riesgo del usuario o el inicio de sesión para bloquear inicios de sesión peligrosos o solicite que el usuario realice la autenticación multifactor. Estas acciones pueden evitar que un atacante pueda aprovechar una identidad robada para causar daños, y pueden darle algún tiempo para proteger la identidad.


## Directiva de seguridad de riesgo de inicio de sesión

Una directiva de seguridad de riesgo de inicio de sesión es una directiva de acceso condicional que evalúa el riesgo en un inicio de sesión específico y aplica mitigaciones de acuerdo con las reglas y condiciones predefinidas. <br><br> ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/700.png "Directiva de riesgo de inicio de sesión") <br>

Azure AD Identity Protection ayuda a administrar la mitigación de inicios de sesión peligrosos al permitir:

- Configurar los usuarios y grupos a los que se aplica la directiva. <br><br> ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/701.png "Directiva de riesgo de inicio de sesión") <br>

- Establecer el umbral de nivel de riesgo del usuario (bajo, medio o alto) que desencadena un desafío de autenticación multifactor para los inicios de sesión afectados. <br><br> ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/702.png "Directiva de riesgo de inicio de sesión") <br>

- Establecer el umbral de nivel de riesgo del usuario (bajo, medio o alto) que bloquea los inicios de sesión afectados. <br><br> ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/703.png "Directiva de riesgo de inicio de sesión") <br>

- Revisar y evaluar el impacto de un cambio antes de activarlo. <br><br> ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/704.png "Directiva de riesgo de inicio de sesión") <br>

 
Elegir un umbral **Alto** reduce el número de veces que una directiva se desencadena y minimiza el impacto en los usuarios.<br> Sin embargo, excluye los usuarios marcados con un riesgo **Bajo** y **Medio** por la directiva, lo que puede no bloquear a un atacante para que no pueda aprovechar una identidad en peligro.

Al establecer la directiva:

- Excluya a los usuarios que no tengan o no puedan la autenticación multifactor.

- Excluya a los usuarios que tengan configuraciones regionales en las que no es práctico habilitar la directiva (por ejemplo, no tienen acceso al departamento de soporte técnico).

- Excluya a los usuarios que es probable que generen una gran cantidad de falsos positivos (desarrolladores, analistas de seguridad).

- Utilice un umbral **Alto** durante la puesta en servicio inicial de la directiva, o bien si debe reducir al mínimo los desafíos vistos por los usuarios finales.

- Utilice un umbral **Bajo** si su organización requiere una mayor seguridad. Seleccionar un umbral **Bajo** presenta desafíos adicionales de inicio de sesión del usuario, pero aumenta la seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones es configurar una regla para un umbral **Medio** con el fin de lograr un equilibrio entre la facilidad de uso y la seguridad.

 
La directiva de riesgo de inicio de sesión:

- Se aplica a todo el tráfico del explorador e inicios de sesión mediante una autenticación moderna.
- No afecta a aplicaciones que utilizan protocolos de seguridad anteriores al deshabilitar el punto de conexión WS-Trust en el IDP federado, como ADFS.

La página **Eventos de riesgo** de la consola de Identity Protection enumera todos los eventos:

- A los que se aplicó esta directiva
- Puede revisar la actividad y determinar si la acción era apropiada o no. 




## Directiva de registro de la autenticación multifactor

La autenticación multifactor se usa para obtener seguridad adicional de una identidad del usuario.<br> El registro para la autenticación multifactor es un paso crítico para preparar su organización para proteger y recuperar cuentas puestas en peligro. <br> ![Registro MFA](./media/active-directory-identityprotection/600.png "Registro MFA") <br>

Azure AD Identity Protection ayuda a administrar la puesta en servicio del registro de la autenticación multifactor, ya que permite:

- Configurar los usuarios y grupos a los que se aplica la directiva. <br><br> ![Registro MFA](./media/active-directory-identityprotection/601.png "Registro MFA") <br>

- Definir cuánto tiempo pueden omitir el registro. <br><br> ![Registro MFA](./media/active-directory-identityprotection/602.png "Registro MFA") <br>

- Ver el estado actual del registro de los usuarios afectados. <br><br> ![Registro MFA](./media/active-directory-identityprotection/603.png "Registro MFA") <br>


##Flujos de mitigación del riesgo de inicios de sesión 

#### Flujo de recuperación de inicios de sesión peligrosos

Cuando un administrador ha configurado una directiva de riesgo de inicio de sesión, se notifica a los usuarios afectados cuando intentan iniciar sesión.

El flujo de inicio de sesión peligroso consta de dos pasos:

1. Se informa al usuario que se ha detectado la algo inusual en su inicio de sesión, como un inicio de sesión desde una nueva ubicación, dispositivo o aplicación. <br> ![Corrección](./media/active-directory-identityprotection/120.png "Corrección") <br>

2. Para demostrar su identidad, el usuario debe resolver un desafío de seguridad. Si el usuario está registrado para la autenticación multifactor necesitan devolver un código de seguridad enviado a su número de teléfono. Puesto que esto solo es un inicio de sesión peligroso, no una cuenta en peligro, el usuario no tendrá que cambiar la contraseña en este flujo. <br> ![Corrección](./media/active-directory-identityprotection/121.png "Corrección") <br>
 
#### Inicios de sesión peligrosos bloqueados
Los administradores también pueden elegir establecer una directiva de seguridad de riesgo de inicio de sesión para bloquear a los usuarios al iniciar sesión en función del nivel de riesgo. Para ser desbloqueados, los usuarios finales deben ponerse en contacto con un administrador o el departamento de soporte técnico, o bien pueden intentar iniciar sesión desde una ubicación o dispositivo conocidos. La recuperación automática al resolver la autenticación multifactor no es una opción en este caso.

<br> ![Corrección](./media/active-directory-identityprotection/130.png "Corrección") <br>
 
#### Registro de la autenticación multifactor

La mejor experiencia de usuario en ambos casos: el flujo de recuperación de la cuenta en peligro y el flujo de inicio de sesión peligroso, es cuando el usuario puede realizar su recuperación. Si un usuario está registrado para la autenticación multifactor, ya tiene un número de teléfono asociado a su cuenta que puede usar para pasar desafíos de seguridad. No es necesaria ninguna participación del administrador o el departamento de soporte técnico para recuperar la cuenta puesta en peligro. Por lo tanto, se recomienda encarecidamente a los usuarios que se registren en la autenticación multifactor.

Los administradores pueden:

- Establecer una directiva que requiere que los usuarios configuren sus cuentas para una verificación de seguridad adicional. 
- Permitir que se omita el registro de la autenticación multifactor durante 30 días, en caso de que deseen dar a los usuarios un período de gracia antes de registrarse.

 
 <br> ![Corrección](./media/active-directory-identityprotection/140.png "Corrección") <br> <br> ![Corrección](./media/active-directory-identityprotection/141.png "Corrección") <br> <br> ![Corrección](./media/active-directory-identityprotection/142.png "Corrección") <br>

 

#### Registro de la autenticación multifactor durante un inicio de sesión peligroso

Es importante que los usuarios se registren en la autenticación multifactor para que están preparados y puedan pasar desafíos de seguridad. Si un usuario no está registrado en la autenticación multifactor, pero la directiva requiere que lo esté, se pedirá que se registre durante un inicio de sesión de peligroso. Esto significa que se le podría solicitar a un atacante que agregue un número de teléfono, en lugar de al usuario correcto. <br> Para evitar esta situación, solicite a los usuarios que se registren en la autenticación multifactor tan pronto como sea posible, para que ya haya un número de teléfono asociado a su cuenta en caso de que alguna vez puedan estar en peligro. Como alternativa, los administradores pueden bloquear completamente a los usuarios puestos en peligro que no están registrados en la autenticación multifactor.

 <br> ![Corrección](./media/active-directory-identityprotection/150.png "Corrección") <br> <br> ![Corrección](./media/active-directory-identityprotection/151.png "Corrección") <br>






 
## Guía

### Simulación de eventos de riesgo

En esta sección se proporciona información general de la simulación de los siguientes tipos de eventos de riesgo:

- Inicios de sesión desde direcciones IP anónimas (fácil)

- Inicios de sesión desde ubicaciones desconocidas (moderada)

- Viaje imposible a ubicaciones inusuales (difícil)

Otros eventos de riesgo no se pueden simular de forma segura.


#### Inicios de sesión desde direcciones IP anónimas

Este tipo de evento de riesgo identifica los usuarios que han iniciado sesión correctamente desde una dirección IP que se ha identificado como una dirección IP de proxy anónima. A menudo, estos servidores proxy los usan los usuarios que desean ocultar la dirección IP del dispositivo y es posible que se usen con fines malintencionados.

Para simular un inicio de sesión desde una IP anónima, siga estos pasos:

1.	Descargue el [explorador Tor](https://www.torproject.org/projects/torbrowser.html.en)
2.	Con el explorador Tor, vaya a https://myapps.microsoft.com   
3.	Escriba las credenciales de la cuenta que desee que aparezcan en el informe de inicios de sesión desde direcciones IP anónimas

¡Ya ha terminado! El inicio de sesión debe estar visible en Identity Protection en 5 minutos.


####Inicios de sesión desde ubicaciones desconocidas
El riesgo de ubicaciones desconocidas es un mecanismo de evaluación de inicios de sesión en tiempo real que tiene en cuenta las ubicaciones de inicio de sesión anteriores (IP, latitud/longitud y ASN) para determinar las ubicaciones nuevas o desconocidas. El sistema almacena las direcciones IP, la latitud/longitud y las ANS anteriores, y considera que son ubicaciones "conocidas". Una ubicación de inicio de sesión se considera desconocida si la ubicación de inicio de sesión no coincide con ninguna de las ubicaciones conocidas existentes. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual no marca ninguna nueva ubicación como ubicación desconocida. El sistema también omite los inicios de sesión desde dispositivos conocidos y ubicaciones geográficamente cercanas a una ubicación familiar existente

Para simular ubicaciones desconocidas, tendrá que iniciar sesión desde una ubicación y un dispositivo desde los que no se haya iniciado sesión en esa cuenta antes. Paso a paso:

1.	Elija una cuenta que tenga al menos un historial de inicios de sesión de 14 días 

2.	Realice uno de estos pasos:
	
    a. Mientras usa una VPN, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com) y escriba las credenciales de la cuenta para la que desea simular el evento de riesgo.

    b. Pida a un colaborador en otra ubicación que inicie sesión con las credenciales de la cuenta (no recomendado)

¡Ya ha terminado! El inicio de sesión debe estar visible en Identity Protection en 5 minutos.
 
#### Viaje imposible a una ubicación inusual
Simular la condición de viaje imposible es complicado porque el algoritmo utiliza el aprendizaje automático para descartar falsos positivos, como un viaje imposible desde dispositivos conocidos o inicios de sesión de VPN usadas por otros usuarios del directorio. Además, el algoritmo requiere un historial de inicios de sesión del usuario de 3 a 14 días antes de que comience a generar eventos de riesgo.

1.	Mediante el explorador estándar, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2.	Escriba las credenciales de la cuenta para la que desea generar un evento de riesgo de viaje imposible

3.	Ahora cambie el agente de usuario. Puede cambiar el agente de usuario en Internet Explorer desde Herramientas de desarrollo, o bien en Firefox o Chrome con un complemento modificador del agente de usuario.

4.	Ahora cambie la dirección IP. Puede cambiar la dirección IP mediante una VPN, un complemento de Tor, o iniciando una máquina nueva en Azure en otro centro de datos.

5.	Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com) con las mismas credenciales que antes y pocos minutos después del inicio de sesión anterior.

El inicio de sesión debe estar visible en Identity Protection en 2-4 horas. Sin embargo, debido a los complejo modelos de aprendizaje automático implicados, es posible que no se detecte. Sería una buena idea replicar estos pasos para varias cuentas de Azure AD.


#### Simulación de puntos vulnerables 
Los puntos vulnerables son puntos débiles de un entorno de Azure AD que puede ser aprovechados por un actor perjudicial. Actualmente se exponen 3 tipos de puntos vulnerables en Azure AD Identity Protection que aprovechan otras características de Azure AD. Estos puntos vulnerables se mostrará en Identity Protection automáticamente una vez configuradas estas características.

-	¿Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)?
-	[Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) de Azure AD.
-	[Privileged Identity Management](active-directory-privileged-identity-management-configure.md) de Azure AD. 



###Directivas de acceso condicional basadas en riesgos
####Riesgo de compromiso de usuario

**Para probar el riesgo de compromiso de usuario, realice los pasos siguientes**:

1.	Inicie sesión en [https://portal.azure.com](https://portal.azure.com) con las credenciales de administrador global para el inquilino.

2.	Vaya a **Identity Protection**.

3.	En la hoja principal de Azure AD Identity Protection, haga clic en **Configuración**.

4.	En la hoja **Configuración del portal**, en **Reglas de seguridad**, haga clic en **Riesgo de compromiso de usuario**.

5.	En la hoja **Riesgo de inicio de sesión**, desactive **Habilitar regla** y, a continuación, haga clic en **Guardar** la configuración.

6.	Para una cuenta de usuario determinada, simule un evento de riesgo de ubicaciones desconocidas o IP anónima. Esto eleva el nivel de riesgo del usuario para ese usuario a **Medio**.

7.	Espere unos minutos y compruebe que el nivel de usuario para el usuario es **Medio**.

8.	Vaya a la hoja **Configuración del portal**.

9.	En la hoja **Riesgo de compromiso de usuario**, seleccione **Activado** en **Habilitar regla**.

10.	Seleccione una de las siguientes opciones:

    a. Para bloquearlo, seleccione **Medio** en **Bloquear inicio de sesión**

    b. Para aplicar el cambio de contraseña segura, seleccione **Medio** en **Requerir autenticación multifactor**.

13.	Haga clic en **Guardar**.

14. Ahora puede probar el acceso condicional basado en riesgos mediante un inicio de sesión con un usuario con un nivel de riesgo elevado. Si el riesgo del usuario es Medio, se bloqueará el inicio de sesión o se forzará el cambio de contraseña, en función de la directiva que establezca. <br><br> ![Guía](./media/active-directory-identityprotection/201.png "Guía") <br>

 
####Riesgo de inicio de sesión

 


Para probar el riesgo de inicio de sesión, realice los pasos siguientes:

1.	Inicie sesión en [https://portal.azure.com](https://portal.azure.com) con las credenciales de administrador global para el inquilino.

2.	Vaya a **Identity Protection**.

3.	En la hoja principal de **Azure AD Identity Protection**, haga clic en **Configuración**.

4.	En la hoja **Configuración del portal**, en **Reglas de seguridad**, haga clic en **Riesgo de inicio de sesión**.

5.	En la hoja **Riesgo de inicio de sesión**, seleccione **Activado** en **Habilitar regla**.

7.	Seleccione una de las siguientes opciones:

    a. Para bloquearlo, seleccione **Medio** en **Bloquear inicio de sesión**

    b. Para aplicar el cambio de contraseña segura, seleccione **Medio** en **Requerir autenticación multifactor**.

8.	Para bloquearlo, seleccione Medio en Bloquear inicio de sesión...

9.	Para aplicar la autenticación multifactor, seleccione Medio en Requerir autenticación multifactor...

10.	Haga clic en **Save** (Guardar).

11.	Ahora puede probar el acceso condicional basado en riesgos mediante la simulación de los eventos de riesgo de IP anónima o ubicaciones desconocidas, que se consideran eventos de riesgo Medio.

<br> ![Guía](./media/active-directory-identityprotection/200.png "Guía") <br>


## Consulte también

 - [Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview) (Presentación de Azure AD e Identity: versión preliminar de Identity Protection)
 - [Glosario de Identity Protection](active-directory-identityprotection-glossary.md)

<!---HONumber=AcomDC_0302_2016-->