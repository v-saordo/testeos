 <properties
   pageTitle="Tipos de notificaciones y tokens admitidos | Microsoft Azure"
   description="Una guía en la que se describen y evalúan las notificaciones en los tokens SAML 2.0 y los tokens web JSON (JWT) emitidos por Azure Active Directory (AAD)"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/08/2016"
   ms.author="mbaldwin"/>

# Tipos de notificaciones y tokens admitidos

Este tema está diseñado para ayudarle a entender y evaluar las notificaciones en los tokens SAML 2.0 y tokens web JSON (JWT) que emite Azure Active Directory (Azure AD).

El tema empieza con una descripción de cada notificación de token y muestra un ejemplo de la notificación en un token SAML y un token JWT token, según corresponda. Las notificaciones que se encuentran en estado de vista previa se enumeran por separado. El tema finaliza con tokens de ejemplo para que pueda ver las notificaciones en contexto.

Azure agrega notificaciones a los tokens con el tiempo para permitir escenarios nuevos. Normalmente, presentamos estas notificaciones en estado de vista previa y, a continuación, las convertimos para que sean totalmente compatibles tras un período de prueba. Para prepararse para los cambios de notificación, las aplicaciones que aceptan tokens de Azure AD deben omitir notificaciones de token desconocidas para que las notificaciones nuevas no interrumpan la aplicación. Las aplicaciones que usan notificaciones que se encuentran en estado de vista previa no deberían depender de estas notificaciones y no deberían generar excepciones si la notificación no aparece en el token. Si la aplicación necesita notificaciones que no están disponibles en los tokens SAML o JWT que emite Azure AD, consulte la sección Adiciones de la Comunidad que se encuentra en la parte inferior de esta página para sugerir y debatir sobre nuevos tipos de notificación.

## Referencia de notificaciones de token

En esta sección se enumeran y describen las notificaciones en tokens que devuelve Azure AD. Incluye la versión SAML y la versión JWT de la notificación, así como una descripción de la notificación y su uso. Las notificaciones se muestran en orden alfabético.

### Identificador de aplicación

La notificación de id. de aplicación identifica la aplicación que usa el token de acceso a un recurso. La aplicación puede actuar por sí misma o en nombre de un usuario. Normalmente, el id. de aplicación representa un objeto de aplicación, pero también puede representar un objeto de entidad de servicio en Azure AD.

Azure AD no admite una notificación de id. de aplicación en un token SAML.

En un token JWT, el id. de aplicación aparece en una notificación appid.

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### Público
La audiencia de token es el destinatario previsto del token. La aplicación que recibe el token debe comprobar que el valor de la audiencia sea correcto y rechazar cualquier token destinado a una audiencia diferente.

El valor de la audiencia es una cadena, por lo general, la dirección base del recurso al que se tiene acceso, por ejemplo, "https://contoso.com". En los tokens de Azure AD, la audiencia es el URI de id. de aplicación de la aplicación que solicitó el token. Cuando la aplicación (que es la audiencia) tiene más de un URI de id. de aplicación, el URI de id. de aplicación que se incluye en la notificación de la audiencia del token coincide con el URI de id. de aplicación que se encuentra en la solicitud de token. En un token SAML, la notificación de la audiencia se define en el elemento Audience del elemento AudienceRestriction.

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

En un token JWT, la audiencia aparece en una notificación aud.

    "aud":"https://contoso.com"

### Referencia de clase de contexto de autenticación de aplicación

La notificación de la referencia de clase de contexto de autenticación de aplicación indica cómo se autenticó el cliente. Para un cliente público, el valor es 0. Si se usan el id. de cliente y el secreto de cliente, el valor es 1.

En un token JWT, el valor de referencia de clase de contexto de autenticación aparece en una notificación appidacr (valor de ACR específico de la aplicación).

    "appidacr": "0"

### Referencia de clase de contexto de autenticación
La notificación de la referencia de clase de contexto de autenticación indica cómo se autenticó el firmante, en lugar del cliente, como es el caso en la notificación de la referencia de clase de contexto de autenticación de aplicación. Un valor de "0" indica que la autenticación del usuario final no ha cumplido los requisitos de ISO/IEC 29115.

- En un token JWT, el valor de referencia de clase de contexto de autenticación aparece en una notificación acr (valor de ACR específico del usuario).

    "acr": "0"

### Instante de autenticación

La notificación del instante de autenticación registra la fecha y hora en que se produjo la autenticación.

En un token SAML, el instante de autenticación aparece en el atributo AuthnInstant del elemento AuthnStatement. Representa una fecha y hora en hora UTC (Z).

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

Azure AD no tiene una notificación equivalente en tokens JWT.

### Método de autenticación

La notificación del método de autenticación le indica cómo se autenticó el firmante del token. En este ejemplo, el proveedor de identidades usa una contraseña para autenticar al usuario. http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

En un token SAML, el valor del método de autenticación aparece en el elemento AuthnContextClassRef.

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

En un token JWT, el valor del método de autenticación aparece dentro de la notificación amr.

    “amr”: ["pwd"]

###Nombre

La notificación de nombre o "nombre dado" proporciona el nombre de pila o "dado" del usuario, tal como se establece en el objeto de usuario de Azure AD.

En un token SAML, el nombre (o "nombre dado") aparece en una notificación en el elemento givenname SAML Attribute.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

En un token JWT, el nombre aparece en la notificación given\_name.

    "given_name": "Frank"

### Grupos

La notificación de grupos proporciona id. de objeto que representan la pertenencia a grupos del firmante. Estos valores son únicos (vea el id. de objeto) y se pueden usar de forma segura para administrar el acceso, por ejemplo, para exigir autorización para tener acceso a un recurso. Los grupos incluidos en la notificación de grupos se configuran por aplicación mediante la propiedad "groupMembershipClaims" del manifiesto de aplicación. Un valor null excluirá todos los grupos, un valor de "SecurityGroup" incluirá únicamente la pertenencia a grupos de seguridad de Active Directory y un valor de "All" incluirá grupos de seguridad y listas de distribución de Office 365. En cualquier configuración, la notificación de grupos representa la pertenencia a grupos transitiva del firmante.

En un token SAML, la notificación de grupos aparece en el atributo de grupos.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

En un token SAML, la notificación de grupos aparece en la notificación de grupos.

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### Proveedor de identidades

La notificación del proveedor de identidades registra el proveedor de identidades que autenticó al firmante del token. Este valor es idéntico al valor de la notificación del emisor, a menos que la cuenta de usuario esté en un inquilino diferente que el emisor.

En un token SAML, el proveedor de identidades aparece en una notificación en el elemento identityprovider SAML Attribute.

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

En un token JWT, el proveedor de identidades aparece en una notificación idp.

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

La notificación IssuedAt almacena la hora a la que se emitió el token. A menudo se usa para medir la actualización de tokens. En un token SAML, el valor IssuedAt aparece en la aserción IssueInstant.

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

En un token JWT, el valor IssuedAt aparece en la notificación iat. El valor se expresa en el número de segundos desde 1970-01-010:0:0Z en hora universal coordinada (UTC).

    "iat": 1390234181

### Emisor

La notificación del emisor identifica el servicio de token de seguridad (STS) que construye y devuelve el token y el inquilino de directorio de Azure AD Directory. En los tokens que devuelve Azure AD, el emisor es sts.windows.net. El GUID en el valor de notificación del emisor es el id. de inquilino del directorio de Azure AD. El id. de inquilino es un identificador inmutable y confiable del directorio.

En un token SAML, la notificación del emisor aparece en un elemento del emisor.

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

En un token JWT, el emisor aparece en una notificación iss.

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### Apellido

La notificación de apellido proporciona el apellido del usuario según se define en el objeto de usuario de Azure AD. En un token SAML, el apellido aparece en una notificación en el elemento surname SAML Attribute.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

En un token JWT, el apellido aparece en la notificación family\_name.

    "family_name": "Miller"

### Nombre

La notificación de nombre proporciona un valor en lenguaje natural que identifica al firmante del token. No se asegura que este valor sea único dentro de un inquilino y está diseñado para usarse solo con fines de visualización. En un token SAML, el nombre aparece en el atributo Name.

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

En una notificación JWT, el nombre aparece en la notificación unique\_name.

    "unique_name": "frankm@contoso.com"

### Id. de objeto

La notificación de id. de objeto contiene un identificador único de un objeto en Azure AD. Este valor es inmutable y no se puede reasignar o volver a usar, por lo que puede usarlo para realizar comprobaciones de autorización de forma segura, por ejemplo, cuando se usa el token para tener acceso a un recurso. Use el id. de objeto para identificar un objeto en las consultas a Azure AD. En un token SAML, el id. de objeto aparece en el atributo objectidentifier.

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

En un token JWT, el id. de objeto aparece en una notificación oid.

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### Roles

La notificación de roles proporciona cadenas descriptivas que representan asignaciones de roles de aplicación del firmante en Azure AD y se puede usar para exigir el control de acceso basado en roles. Los roles de aplicación se definen por aplicación mediante la propiedad "appRoles" del manifiesto de aplicación. La propiedad "value" de cada rol de aplicación es el valor que aparece en la notificación de roles. Los roles incluidos en la notificación de roles representan todos los roles de aplicación que se han concedido al firmante directa e indirectamente a través de la pertenencia a grupos. En un token SAML, la notificación de roles aparece en el atributo de roles.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

En un token JWT, la notificación de roles aparece en el atributo de roles.

    “roles”: ["Admin", … ]

### Scope

El ámbito del token indica los permisos de suplantación concedidos a la aplicación cliente. El permiso predeterminado es user\_impersonation. El propietario del recurso protegido puede registrar valores adicionales en Azure AD.

En un token JWT, el ámbito del token se especifica en una notificación scp.

    "scp": "user_impersonation"

### Asunto

El firmante del token es la entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar o volver a usar, por lo que se puede usar para realizar comprobaciones de autorización de forma segura, por ejemplo, cuando se usa el token para tener acceso a un recurso. Dado que el firmante siempre está presente en los tokens que emite Azure AD, se recomienda usar este valor en un sistema de autorización de propósito general.

En un token SAML, el firmante del token se especifica en el elemento NameID del elemento Subject. El NameID es un identificador único, no reutilizado del firmante, que puede ser un usuario, una aplicación o un servicio.

SubjectConfirmation no es una notificación. Describe cómo se comprueba el firmante del token. "Bearer" indica que el sujeto se confirma por su posesión del token.

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

En un token JWT, el firmante aparece en una notificación sub.

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### Id. de inquilino
El id. de inquilino es un identificador inmutable, no reutilizable que identifica al inquilino de directorio que emitió el token. Puede usar este valor para tener acceso a recursos de directorio específicos del inquilino en una aplicación multiempresa. Por ejemplo, puede usar este valor para identificar al inquilino en una llamada a API Graph.

En un token SAML, el id. de inquilino aparece en una notificación en el elemento tenantid SAML Attribute.

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

En un token JWT, el Id. de inquilino aparece en una notificación tid.

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### Vigencia del token
La notificación de vigencia del token define el intervalo de tiempo dentro del cual un token es válido. El servicio que valida el token debe comprobar que la fecha actual está dentro de la vigencia del token. De lo contrario, debe rechazar el token. El servicio puede proporcionar una asignación de hasta cinco minutos más allá del intervalo de vigencia del token para tener en cuenta las diferencias en la hora del reloj ("desfase temporal") entre Azure AD y el servicio.

En un token SAML, la notificación de vigencia del token se define en el elemento Conditions mediante los atributos NotBefore y NotOnOrAfter.

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

En un token JWT, la vigencia del token se define mediante notificaciones nbf (no antes) y exp (tiempo de expiración). El valor de estas notificaciones se expresa en el número de segundos desde 1970-01-010:0:0Z en hora universal coordinada (UTC). Para obtener más información, consulte RFC 3339.

    "nbf":1363289634,
    "exp":1363293234

### Nombre principal de usuario
La notificación de nombre principal de usuario almacena el nombre de usuario de la entidad de seguridad del usuario.

En un token JWT, el nombre principal de usuario aparece en una notificación upn.

    "upn": frankm@contoso.com

### Versión
La notificación de versión almacena el número de versión del token. En un token JWT, la versión aparece en una notificación ver.

    "ver": "1.0"

## Tokens de ejemplo

Esta sección muestra ejemplos de tokens SAML y JWT que Azure AD devuelve. Estos ejemplos le permiten ver las notificaciones en contexto. Token SAML

Este es un ejemplo de token SAML típico.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### Token JWT: suplantación de usuario

Este es un ejemplo de token web JSON (JWT) típico que se usa en un flujo web de suplantación de usuario. Además de las notificaciones, el token incluye un número de versión en **ver** y **appidacr**, la referencia de clase de contexto de autenticación, que indica cómo se autenticó el cliente. Para un cliente público, el valor es 0. Si se ha usado el id. de cliente o el secreto de cliente, el valor es 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##Otras referencias

[Protocolos de autenticación de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

<!---HONumber=AcomDC_0114_2016-->