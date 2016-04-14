<properties
   pageTitle="Publicación de aplicaciones para usuarios individuales en una colección de Azure RemoteApp (versión preliminar) | Microsoft Azure"
   description="Aprenda a publicar aplicaciones para usuarios individuales, en lugar de para grupos en Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/01/2016"
   ms.author="piotrci"/>

# Publicación de aplicaciones para usuarios individuales en una colección de Azure RemoteApp (versión preliminar)

En este artículo se explica cómo publicar aplicaciones para usuarios individuales en una colección de Azure RemoteApp. Esta es una nueva funcionalidad de Azure RemoteApp, actualmente en estado de "versión preliminar privada" disponible solo para seleccionar usuarios pioneros con fines de evaluación.

Originalmente Azure RemoteApp solo permitía una manera de "publicar"aplicaciones: el administrador publicaba las aplicaciones a partir de la imagen y estas eran visibles para todos los usuarios de la colección.

Un escenario común consiste en incluir muchas aplicaciones en una sola imagen e implementar una colección con el fin de reducir los costos de administración. A menudo, no todas las aplicaciones son pertinentes para todos los usuarios: los administradores prefieren publicar aplicaciones para usuarios individuales para que no vean las aplicaciones innecesarias en su fuente de aplicaciones.

Ahora, esto es posible en Azure RemoteApp: actualmente solo como una característica de versión preliminar limitada. Este es un breve resumen de la nueva funcionalidad:

1. Una colección se puede establecer de uno de estos dos modos:
 
  - el "modo de recopilación" original, en el que todos los usuarios de una colección pueden ver todas las aplicaciones publicadas. Este es el modo predeterminado.
  - el nuevo "modo de aplicación", en el que los usuarios solo ven las aplicaciones que se han asignado explícitamente a ellos

2. Por el momento, el modo de aplicación solo se puede habilitar mediante cmdlets de PowerShell de Azure RemoteApp.

  - Si se establece en modo de aplicación, la asignación de usuario en la colección no se puede administrar a través del Portal de Azure. La asignación de usuario se debe administrar mediante cmdlets de PowerShell.

3. Los usuarios solo verán aquellas aplicaciones publicadas directamente para ellos. No obstante, un usuario podría iniciar las demás aplicaciones disponibles en la imagen accediendo a ellas directamente en el sistema operativo.
  - Esta característica no proporciona un bloqueo de aplicaciones seguro, solo limita la visibilidad de la fuente de aplicaciones.
  - Si necesita impedir que los usuarios accedan a todas las aplicaciones, deberá utilizar colecciones independientes.

## Obtención de los cmdlets de PowerShell de Azure RemoteApp

Para probar la nueva funcionalidad de la versión preliminar, debe usar los cmdlets de Azure PowerShell. Actualmente no se puede usar el Portal de administración de Azure para habilitar el modo de publicación de nueva aplicación.

En primer lugar, asegúrese de que tiene el [módulo de Azure PowerShell](../powershell-install-configure.md) instalado.

A continuación, inicie la consola de PowerShell en modo de administrador y ejecute el siguiente cmdlet:

		Add-AzureAccount

Se le pedirá el nombre de usuario y la contraseña de Azure. Cuando haya iniciado sesión, podrá ejecutar los cmdlets de Azure RemoteApp en las suscripciones de Azure.

## Cómo comprobar en qué modo está una colección

Ejecute el siguiente cmdlet:

		Get-AzureRemoteAppCollection <collectionName>

![Comprobación del modo de recopilación](./media/remoteapp-perapp/araacllelvel.png)

La propiedad AclLevel puede tener los valores siguientes:

- Recopilación: el modo de publicación original. Todos los usuarios pueden ver todas las aplicaciones publicadas.
- Aplicación: el nuevo modo de publicación Los usuarios solo pueden ver las aplicaciones publicadas directamente para ellos.

## Cómo cambiar al modo de publicación de aplicaciones

Ejecute el siguiente cmdlet:

		Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Se conservará el estado de publicación de la aplicación: inicialmente, todos los usuarios verán las aplicaciones originales publicadas.

## Cómo enumerar los usuarios que pueden ver una aplicación específica

Ejecute el siguiente cmdlet:

		Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Este cmdlet permite enumerar todos los usuarios que pueden ver la aplicación.

Nota: Puede ver los alias de la aplicación (denominados "appAlias" en la sintaxis anterior) mediante la ejecución de Get-AzureRemoteAppProgram -CollectionName<collectionName>.

## Cómo asignar una aplicación a un usuario

Ejecute el siguiente cmdlet:

		Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

El usuario podrá ver la aplicación en el cliente de Azure RemoteApp y podrá conectarse a ella.

## Cómo quitar una aplicación de un usuario

Ejecute el siguiente cmdlet:

		Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## Envío de comentarios
Agradecemos sus comentarios y sugerencias sobre esta característica de la versión preliminar. Rellene la [encuesta](http://www.instant.ly/s/FDdrb) para que podamos conocer su opinión.

## ¿No ha tenido la oportunidad de probar la característica en la versión preliminar?
Si no ha participado aún en la versión preliminar, use esta [encuesta](http://www.instant.ly/s/AY83p) para solicitar acceso.

<!---HONumber=AcomDC_0302_2016-->