<properties
   pageTitle="Guía del desarrollador del Almacén de claves | Microsoft Azure"
   description="Los desarrolladores pueden usar el Almacén de clave de Azure para administrar las claves criptográficas en el entorno de Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/19/2016"
   ms.author="bruceper" />

# Guía del desarrollador del Almacén de claves de Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Los desarrolladores pueden usar el Almacén de claves de Azure para administrar las claves criptográficas en el entorno de Microsoft Azure. Almacén de claves admite varios tipos de claves y algoritmos, y puede usarse con módulos de seguridad de hardware (HSM) para claves de alto valor. Además, puede usar el almacén de claves para almacenar de forma segura los secretos que son objetos de octeto de tamaño limitado sin ninguna semántica específica. El control de acceso para los tipos de objetos se administra de forma independiente.

Con la debida autorización, podrá hacer lo siguiente:

- Administrar claves criptográficas mediante [Crear](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Actualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Eliminar](https://msdn.microsoft.com/library/azure/dn903611.aspx) y otras operaciones

- Administrar secretos mediante [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) y otras operaciones

- Usar claves de cifrado con operaciones de [Inicio de sesión](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Comprobar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) y [Cifrar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Descifrar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Las operaciones en los almacenes de claves se autentican y autorizan mediante Azure Active Directory.

## Programación para el almacén de claves

El sistema de administración del almacén de claves para los programadores está compuesto por varias interfaces, con REST como base. [Referencia de la API de REST de Almacén de claves](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentación del SDK de .NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Documentación del SDK de Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Paquete del SDK de .NET](https://azure.microsoft.com/es-ES/documentation/api/)|[Paquete del SDK de Node.js](https://www.npmjs.com/package/azure-keyvault)|

## Administración de almacenes de claves

Los contenedores del Almacén de claves de Azure (almacenes) se pueden administrar mediante REST, PowerShell o la CLI, tal como se describe en los siguientes artículos:

- [Crear y administrar almacenes de claves con CLI](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Crear y administrar almacenes claves con PowerShell](key-vault-get-started.md)
- [Crear y administrar almacenes claves con CLI](key-vault-manage-with-cli.md)


## Procedimientos

Los artículos siguientes proporcionan orientación específica de tareas:

- [Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure](key-vault-hsm-protected-keys.md)

## Ejemplos

- Esta descarga contiene la aplicación de ejemplo HelloKeyVault y un ejemplo de servicio web de Azure. [Ejemplos de código de almacén de claves de Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Use este tutorial como ayuda para aprender a usar el Almacén de claves de Azure desde una aplicación web. [Uso del Almacén de claves de Azure desde una aplicación web](key-vault-use-from-web-application.md)

## Bibliotecas compatibles

- [La biblioteca principal del Almacén de claves de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) proporciona interfaces IKey y IKeyResolver para localizar las claves de identificadores y realizar operaciones con las claves.

- [Las extensiones del Almacén de claves de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) proporcionan capacidades ampliadas para el Almacén de claves de Azure.

<!---HONumber=AcomDC_0211_2016-->