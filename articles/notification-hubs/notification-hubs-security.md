<properties
	pageTitle="Seguridad para Centros de notificaciones"
	description="En este tema se explica la seguridad de los Centros de notificaciones de Azure."
	services="notification-hubs"
	documentationCenter=".net"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="wesmc"/>

#Seguridad

##Información general

En este tema se describe el modelo de seguridad de los Centros de notificaciones de Azure. Dado que los centros de notificaciones son una entidad de Bus de servicio, implementan el mismo modelo de seguridad que el Bus de servicio. Para obtener más información, consulte los temas de [autenticación del Bus de servicio](https://msdn.microsoft.com/library/azure/dn155925.aspx).

##Seguridad de Firma de acceso compartido (SAS) 

Los Centros de notificaciones implementan un esquema de seguridad de nivel de entidad llamado SAS (Firma de acceso compartido). Este esquema permite a las entidades de mensajería declarar hasta 12 reglas de autorización en su descripción que conceden derechos sobre esa entidad.

Cada regla contiene un nombre, un valor de clave (secreto compartido) y un conjunto de derechos, tal como se explica en la sección "Notificaciones de seguridad". Al crear un Centro de notificaciones, automáticamente se crean dos reglas: una con derechos de escucha (que usa la aplicación cliente) y otra con todos los derechos (que usa el back-end de la aplicación).

Al realizar la administración de registros desde aplicaciones cliente, si la información enviada a través de notificaciones no es confidencial (por ejemplo, actualizaciones del información meteorológica), una forma común de acceder a un Centro de notificaciones es dar al valor de clave de la regla acceso de solo escucha a la aplicación cliente y proporcionar al valor de clave de la regla acceso completo al back-end de la aplicación.

No se recomienda insertar el valor de clave en aplicaciones cliente de Tienda Windows. Una manera de evitar insertar el valor de clave es que la aplicación cliente lo recupere del back-end de la aplicación en el inicio.

Es importante comprender que la clave con acceso de escucha permite a una aplicación cliente registrarse para cualquier etiqueta. Si la aplicación debe restringir los registros en etiquetas específicas a clientes específicos (por ejemplo, si las etiquetas representan identificadores de usuario), el back-end de la aplicación debe realizar los registros. Para obtener más información, consulte Administración de registros. Tenga en cuenta que, de este modo, la aplicación cliente no tendrá acceso directo a los Centros de notificaciones.

##Notificaciones de seguridad

De modo similar a otras entidades, las operaciones del Centro de notificaciones se permiten para tres notificaciones de seguridad: escucha, envío y administración.

| Notificación | Descripción | Operaciones permitidas |
|-------|-------------|--------------------|
| Escuchar | Crear o actualizar, leer y eliminar registros únicos | Crear o actualizar registro<br><br>Leer registro<br><br>Leer todos los registros de un identificador<br><br>Eliminar registro |
| Los métodos Send | Enviar mensajes al Centro de notificaciones | Enviar mensaje |
| Manage | CRUD en los Centros de notificaciones (incluida la actualización de las credenciales de PNS y claves de seguridad) y registros de lectura basados en etiquetas | Crear/Actualizar/Eliminar Centros de notificaciones<br><br>Leer registros por etiqueta |


Los Centros de notificaciones aceptan notificaciones concedidas por tokens de Control de acceso de Microsoft Azure y tokens de firma generados con claves compartidas configuradas directamente en el Centro de notificaciones.

<!---HONumber=AcomDC_1203_2015-->