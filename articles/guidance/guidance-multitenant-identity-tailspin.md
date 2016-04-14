<properties
   pageTitle="Acerca de la aplicación Tailspin Surveys | Microsoft Azure"
   description="Información general acerca de la aplicación Tailspin Surveys"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Acerca de la aplicación Tailspin Surveys

Este artículo [forma parte de una serie]. También hay una [aplicación de ejemplo] completa que acompaña a esta serie.

Tailspin es una compañía ficticia que está desarrollando una aplicación SaaS llamada Surveys. Esta aplicación permite a las organizaciones crear y publicar encuestas en línea.

- Una organización puede suscribirse a la aplicación.
- Después de que la organización se ha suscrito, los usuarios pueden iniciar sesión en la aplicación con las credenciales de la organización.
- Los usuarios pueden crear, editar y publicar encuestas.

> [AZURE.NOTE] Para empezar a trabajar con la aplicación, consulte [Running the Surveys application (Ejecución de la aplicación Surveys)].

Esta captura de pantalla muestra la página Edit Survey (Editar encuesta):

![Editar encuesta](media/guidance-multitenant-identity/edit-survey.png)

Observe que el usuario ha iniciado sesión con su identidad organizativa, `bob@contoso.com`.

Los usuarios pueden ver las encuestas creadas por otros usuarios del mismo inquilino.

![Encuestas de inquilinos](media/guidance-multitenant-identity/tenant-surveys.png)

Cuando un usuario crea una encuesta, puede invitar a otras personas a ser colaboradores en la misma. Los colaboradores pueden modificar la encuesta, pero no pueden eliminarla ni publicarla.

![Agregar colaborador](media/guidance-multitenant-identity/add-contributor.png)

Un usuario puede agregar colaboradores de otros inquilinos, lo que permite compartir recursos entre inquilinos. En esta captura de pantalla, Bob (`bob@contoso.com`) está agregando a Alice (`alice@fabrikam.com`) como colaboradora a una encuesta que él mismo creó.

Cuando Alice inicia sesión, ve la encuesta que aparece en la lista de "Surveys I can contribute to" (Encuestas en las que puedo colaborar).

![Colaborador de la encuesta](media/guidance-multitenant-identity/contributor.png)

Tenga en cuenta que Alice inicia sesión en su propio inquilino, no como un invitado del inquilino Contoso. Alice dispone de permisos de colaborador solo para esa encuesta pero no puede ver otras encuestas del inquilino Contoso.

## Arquitectura

La aplicación Surveys consta de un front-end web y un back-end de API web. Ambos se implementan mediante [ASP.NET Core 1.0].

La aplicación web utiliza Azure Active Directory (Azure AD) para autenticar a los usuarios. La aplicación web también llama a Azure AD para obtener tokens de acceso de OAuth 2 para la API Web. Los tokens de acceso se almacenan en Caché en Redis de Azure. La memoria caché habilita a varias instancias para compartir la misma caché de token (por ejemplo, en una granja de servidores).

![Arquitectura](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[forma parte de una serie]: guidance-multitenant-identity.md
[Running the Surveys application (Ejecución de la aplicación Surveys)]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET Core 1.0]: https://docs.asp.net/en/latest/
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->