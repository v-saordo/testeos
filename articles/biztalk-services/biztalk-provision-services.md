<properties
	pageTitle="Crear Servicios de BizTalk de Azure en el Portal de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo aprovisionar o crear Servicios de BizTalk en el Portal de Azure; MABS, WABS"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="mandia"/>



# Creación de Servicios de BizTalk mediante el Portal de Azure

Creación de Servicios de BizTalk de Azure en el Portal de Azure | Microsoft Azure

> [AZURE.TIP] Para iniciar sesión en el Portal de Azure, necesita una suscripción a Azure y una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Consulte [Evaluación gratuita de Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## Crear un Servicio de BizTalk
En función de la edición que elija, puede que no estén disponibles todos los valores del Servicios de BizTalk.

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En la parte inferior del panel de navegación, seleccione **NEW**: ![Seleccione el botón New][NEWButton]

3. Seleccione **SERVICIOS DE APLICACIONES** > **SERVICIO DE BIZTALK** > **CREACIÓN PERSONALIZADA**: ![Seleccione Servicio de BizTalk y seleccione Custom Create][NewBizTalkService]

4. Escriba la configuración del servicio de BizTalk.

	<table border="1">
	<tr>
	<td><strong>Nombre del servicio de BizTalk</strong></td>
	<td>Puede escribir cualquier nombre pero sea específico. Estos son algunos ejemplos:<br/><br/>
	<em>miempresa</em>.biztalk.windows.net<br/>
	<em>miempresamiaplicación</em>.biztalk.windows.net<br/>
	<em>miaplicación</em>.biztalk.windows.net<br/><br/>".biztalk.windows.net" se agregará automáticamente al nombre que escriba. Esto crea una dirección URL que se utiliza para tener acceso a su servicio de BizTalk, como <strong>https://<em>miaplicación</em>.biztalk.windows.net</strong>.
	</td>
	</tr>
	<tr>
	<td><strong>Edición</strong></td>
	<td>Si está en la fase de prueba/desarrollo, elija <strong>Developer</strong>. Si está en la fase de producción, utilice <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Servicios de BizTalk: gráfico de ediciones</a> para determinar si la opción correcta para su escenario de negocios es <strong>Premium</strong>, <strong>Standard</strong> o <strong>Basic</strong>.
	</td>
	</tr>
	<tr>
	<td><strong>Región</strong></td>
	<td>Seleccione la región geográfica donde hospedar su servicio de BizTalk.</td>
	</tr>
	<tr>
	<td><strong>URL de dominio</strong></td>
	<td><strong>Opcional</strong>. De manera predeterminada, la dirección URL de dominio es <em>SuNombreDeServicioDeBizTalk</em>.biztalk.windows.net. También se puede escribir un dominio personalizado. Por ejemplo, si el dominio es <em>contoso</em>, puede escribir: <br/><br/>
	<em>MiEmpresa</em>.contoso.com<br/>
	<em>MiEmpresaMiAplicación</em>.contoso.com<br/>
	<em>MiAplicación</em>.contoso.com<br/>
	<em>SuNombreDeServicioDeBizTalk</em>.contoso.com<br/>
	</td>
	</tr>
	</table>
Seleccione la flecha SIGUIENTE.

5. Escriba la configuración de la base de datos y almacenamiento:

	<table border="1">
	<tr>
	<td><strong>Cuenta de almacenamiento de supervisión y archivado</strong></td>
	<td>Seleccione una cuenta de almacenamiento existente o cree una nueva cuenta de almacenamiento. <br/><br/>Si crea una nueva cuenta de almacenamiento, escriba el <strong>Nombre de cuenta de almacenamiento</strong>.</td>
	</tr>
	<tr>
	<td><strong>Base de datos de seguimiento</strong></td>
	<td>Si usa una Base de datos SQL de Azure existente, no puede usarla otro Servicio de BizTalk. Necesita el nombre de inicio de sesión y la contraseña especificados cuando se creó ese servidor de Base de datos SQL de Azure.<br/><br/><strong>Sugerencia</strong> Cree la base de datos de seguimiento y la cuenta de almacenamiento de supervisión/archivado en la misma región que el Servicio de BizTalk.</td>
	</tr>
	</table>
Seleccione la flecha SIGUIENTE.

6. Escriba la configuración de la base de datos:

	<table border="1">
	<tr>
	<td><strong>Name</strong></td>
	<td>Disponible cuando se selecciona <strong>Crear una nueva instancia de base de datos SQL</strong> en la pantalla anterior.
	<br/><br/>
	Escriba un nombre de Base de datos SQL que utilizará su Servicio de BizTalk.</td>
	</tr>
	<tr>
	<td><strong>Servidor</strong></td>
	<td>Disponible cuando se selecciona <strong>Crear una nueva instancia de base de datos SQL</strong> en la pantalla anterior.
	<br/><br/>
	Seleccione un servidor de Base de datos SQL o cree un nuevo servidor de Base de datos SQL.</td>
	</tr>
	<tr>
	<td><strong>Nombre de inicio de sesión del servidor</strong></td>
	<td>Escriba el nombre de usuario de inicio de sesión.</td>
	</tr>
	<tr>
	<td><strong>Contraseña de inicio de sesión del servidor</strong></td>
	<td>Escriba la contraseña de inicio de sesión.</td>
	</tr>
	<tr>
	<td><strong>Región</strong></td>
	<td>Disponible cuando se selecciona <strong>Crear una nueva instancia de base de datos SQL</strong>. Seleccione la región geográfica donde hospedar su Base de datos SQL.</td>
	</tr>
	</table>

Seleccione la marca de verificación para completar el asistente. Aparece el icono de progreso: ![El icono de progreso aparece cuando se completa][ProgressComplete]

Una vez que se completa, se crea un Servicio de BizTalk de Azure y queda listo para las aplicaciones. La configuración predeterminada es suficiente. Si desea cambiar la configuración predeterminada, seleccione **SERVICIOS DE BIZTALK** en el panel de navegación que se encuentra a la izquierda y seleccione su Servicio de BizTalk. La configuración adicional aparece en las [pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md) de la parte superior.

Según el estado del servicio de BizTalk, hay algunas operaciones que no se pueden completar. Para ver una lista de estas operaciones, vaya al [gráfico del estado de Servicios de BizTalk](biztalk-service-state-chart.md).


## Pasos posteriores al aprovisionamiento

-  [Instalación del certificado en un equipo local](#InstallCert)
-  [Incorporación de un certificado listo para producción](#AddCert)
-  [Obtención del espacio de nombres de control de acceso](#ACS)

#### <a name="InstallCert"></a>Instalación del certificado en un equipo local
Como parte del aprovisionamiento del Servicio de BizTalk, se crea un certificado autofirmado y se asocia a su suscripción del Servicios de BizTalk. Debe descargar este certificado e instalarlo en equipos desde los que implemente aplicaciones del Servicio de BizTalk o envíe mensajes a un extremo del Servicio de BizTalk.

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Seleccione **SERVICIOS DE BIZTALK** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione su suscripción al Servicio de BizTalk.
3. Seleccione la pestaña **Panel**.
4. Seleccione **Descargar certificado SSL**. ![Modificar certificado SSL][QuickGlance]
5. Haga doble clic en el certificado y ejecute el asistente para instalar el certificado. Asegúrese de instalar el certificado en el almacén **Entidades de certificación raíz de confianza**.

#### <a name="AddCert"></a>Incorporación de un certificado listo para producción
El certificado autofirmado que se crea automáticamente al crear Servicios de BizTalk está pensado únicamente para entornos de desarrollo. Para escenarios de producción, reemplácelo por un certificado listo para producción.

1. En la pestaña **Panel**, seleccione **Actualizar certificado SSL**.
2. Vaya al certificado SSL privado (*NombreCertificado*.pfx) que incluye el nombre de su Servicio de BizTalk, escriba la contraseña y haga clic en la marca.

#### <a name="ACS"></a>Obtención del espacio de nombres de control de acceso

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Seleccione **SERVICIOS DE BIZTALK** en el panel de navegación izquierdo y, a continuación, seleccione su Servicio de BizTalk.
3. Seleccione **Información de conexión** en la barra de tareas: ![Seleccione Connection Information][ACSConnectInfo]

4. Copie los valores de control de acceso.

Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso. El espacio de nombres Control de acceso se crea automáticamente para el Servicio de BizTalk.

Los valores de control de acceso se pueden utilizar con cualquier aplicación. Una vez que se crean los Servicios de BizTalk de Azure, el espacio de nombres del servicio de control de acceso controla la autenticación con su implementación del servicio de BizTalk. Si desea cambiar la suscripción o administrar el espacio de nombres, seleccione **ACTIVE DIRECTORY** en el panel de navegación que se encuentra a la izquierda y, a continuación, seleccione el espacio de nombres. La barra de tareas muestra sus opciones.

Haga clic en **Administrar** para abrir el Portal de administración del sistema de control de acceso. En el Portal de administración del control de acceso, el servicio de BizTalk usa **Identidades de servicio**: ![Identidades de servicio de ACS en el Portal de administración del sistema de control de acceso][ACSServiceIdentities]

La identidad de servicio del control de acceso es un conjunto de credenciales que permiten que las aplicaciones o los clientes se autentiquen directamente con el control de acceso.

> [AZURE.IMPORTANT] El servicio de BizTalk utiliza el valor **Owner** para la identidad de servicio predeterminada y el valor **Password**. Si usa el valor de Symmetric Key en lugar del valor de Password, puede producirse el siguiente error:<br/><br/>*No se pudo conectar a la cuenta del servicio de administración de Control de acceso con las credenciales especificadas*

[Administración del espacio de nombres ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) muestra algunas directrices y recomendaciones.

## Requisitos explicados

Estos requisitos no se aplican a la versión gratuita.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Lo que necesita</strong></td>
        <td><strong>Por qué es necesario</strong></td>
</tr>
<tr>
<td>Suscripción de Azure</td>
<td>La suscripción determina quién puede iniciar sesión en el Portal de Azure. El titular de la cuenta crea la suscripción en <a HREF="https://account.windowsazure.com/Subscriptions"> Suscripciones de Azure</a>.
<br/><br/>
La cuenta de Azure puede tener varias suscripciones y la puede administrar alguien que tenga permisos. Por ejemplo, el titular de la cuenta de Azure crea una suscripción llamada <em>BizTalkServiceSubscription</em> y concede acceso a la misma a los administradores de BizTalk dentro de su empresa (por ejemplo, ContosoBTSAdmins@live.com). En este escenario, los administradores de BizTalk inician sesión en el Portal de Azure y tienen derechos de administrador en todos los servicios hospedados en la suscripción, incluidos Servicios de BizTalk de Azure. Los administradores de BizTalk no son los titulares de la cuenta de Azure y, por lo tanto, no tienen acceso a información de facturación alguna.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> Administrar cuentas, suscripciones y roles administrativos en el Portal de Azure</a> ofrece más información.
</td>
</tr>
<tr>
<td>Base de datos SQL de Azure</td>
<td>Almacena las tablas, vistas y procedimientos almacenados que utilizan los Servicios de BizTalk de Azure, incluidos los datos de seguimiento.
<br/><br/>
Cuando cree un servicio de BizTalk, puede usar un servidor de Azure SQL Server existente, una Base de datos SQL de Azure existente, o bien puede crear automáticamente un servidor o una base de datos nuevos.
<br/><br/>
La escala de Base de datos SQL se configura automáticamente. Normalmente, la escala predeterminada es suficiente para un Servicio de BizTalk. El cambio de la escala puede afectar al precio. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930"> Cuentas y facturación en Base de datos SQL de Azure</a>
<br/><br/>
<strong>Notas</strong>
<br/>
<ul>
<li> Cuando crea una nueva Base de datos SQL o SQL Server de Azure, Servicios de Azure se habilita automáticamente. El Servicio de BizTalk requiere que los servicios de Azure estén habilitados.</li>
<li>Si crea una Base de datos SQL de Azure nueva en un SQL Server de Azure existente, no se cambian las reglas de firewall del servidor. Como resultado, es posible que otros servicios de Azure no puedan tener acceso a las bases de datos del servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Espacio de nombres del servicio de control de acceso de Azure</td>
<td>Autentica con los Servicios de BizTalk de Azure. Cuando implementa un proyecto de servicio de BizTalk desde Visual Studio, debe escribir este espacio de nombres del servicio de control de acceso. Al crear un Servicio de BizTalk, se crea automáticamente el espacio de nombres Control de acceso. </td>
</tr>

<tr>
<td>Cuenta de almacenamiento de Azure</td>
<td>Proporciona acceso a tablas, blobs y colas usados por su servicio de BizTalk para guardar lo siguiente:

<ul>
<li>Archivos de registro que supervisan el Servicio de BizTalk. El resultado de la supervisión también aparece en la pestaña **Monitor** en el Portal de Azure.</li>
<li>Cuando se crea un contrato X12 o AS2 entre asociados, puede habilitar la característica de archivado para almacenar propiedades de mensaje. Estos datos se guardan en la cuenta de almacenamiento.</li>
</ul>
<br/>
Cuando crea un servicio de BizTalk, puede usar una cuenta de almacenamiento existente o puede crear automáticamente una nueva.
<br/><br/>
La configuración de almacenamiento predeterminada es suficiente para un Servicio de BizTalk.
<br/><br/>
Cuando crea una cuenta de almacenamiento, se crean automáticamente una clave primaria y una clave secundaria. Estas claves controlan el acceso a su cuenta de almacenamiento. El servicio de BizTalk utiliza automáticamente la clave principal.
<br/><br/>
Vea <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Almacenamiento</a> para obtener más información.
</td>
</tr>

<tr>
<td>Certificado privado de SSL</td>
<td>
Cuando se crea un servicio de Azure BizTalk, también se crea una URL HTTPS que incluye su nombre de Servicio de BizTalk. Esta URL se configura automáticamente para usar un certificado solo de desarrollo autofirmado. Para producción, necesita un certificado SSL privado.
<br/><br/>
<strong>Información importante sobre el certificado SSL</strong>

<ul>
<li>La fecha de caducidad del certificado debe ser menor a cinco años.</li>
<li>Todos los certificados privados requieren una contraseña. Apréndase esta contraseña y, como procedimiento recomendado, comparta esta contraseña con sus administradores.</li>
<li>Los certificados autofirmados se utilizan en un entorno de prueba o de desarrollo. Cuando utilice certificados autofirmados, importe el certificado a su almacén personal de certificados y al almacén de certificados de las entidades de certificación raíz de confianza.</li>
</ul>
<br/>Cuando envíe la solicitud de certificado de producción a su entidad de certificación, proporcione las siguientes propiedades del certificado:
<br/>

<ul>
<li><strong>Uso mejorado de clave</strong>: como mínimo, Servicios de BizTalk de Azure requiere la autenticación de servidor.</li>
<li><strong>Nombre común</strong>: escriba el nombre de dominio completo (FQDN) de la URL de los Servicios de BizTalk de Azure. Consulte <a HREF="#BizTalk">Crear un Servicio de BizTalk</a> en este artículo.</li>
</ul>
<br/>
Es posible agregar un certificado nuevo o distinto después de crear el servicio de BizTalk.
</td>
</tr>
</table>



## conexiones híbridas

Cuando cree un Servicio de BizTalk de Azure, la pestaña **Conexiones híbridas** estará disponible:

![Pestaña Conexiones híbridas][HybridConnectionTab]

Las conexiones híbridas se usan para conectar un sitio web de Azure o un servicio móvil de Azure a cualquier recurso local que usa un puerto TCP estático, como SQL Server, MySQL, API web HTTP, Servicios móviles y la mayoría de los servicios web personalizados. Las conexiones híbridas y el servicio de adaptador de BizTalk son diferentes. El Servicio de adaptador de BizTalk se usa para conectar los Servicios de BizTalk de Azure a un sistema de línea de negocio (LOB) local.

 Vea [Conexiones híbridas](integration-hybrid-connection-overview.md) para obtener más información, incluida la creación y la administración de conexiones híbridas.


## Pasos siguientes

Ahora que se crea un servicio de BizTalk, familiarícese con [Servicios de BizTalk: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md). Su servicio de BizTalk está listo para las aplicaciones. Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Consulte también
- [Servicios de BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md)<br/>
- [Servicios de BizTalk: gráfico del estado](biztalk-service-state-chart.md)<br/>
- [Servicios de BizTalk: copias de seguridad y restauración](biztalk-backup-restore.md)<br/>
- [Servicios de BizTalk: limitaciones](biztalk-throttling-thresholds.md)<br/>
- [Servicios de BizTalk: nombre del emisor y clave del emisor](biztalk-issuer-name-issuer-key.md)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Conexiones híbridas](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

<!---HONumber=AcomDC_0302_2016-->