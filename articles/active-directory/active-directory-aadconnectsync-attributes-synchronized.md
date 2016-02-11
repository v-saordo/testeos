<properties
	pageTitle="Azure AD Connect Sync: atributos sincronizados con Azure Active Directory | Microsoft Azure"
	description="Enumera los atributos que se sincronizan con Azure Active Directory."
	services="active-directory"
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
	ms.date="01/21/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: atributos sincronizados con Azure Active Directory

En este tema se enumera los atributos que se sincronizan mediante Azure AD Connect Sync.<br> Los atributos se agrupan por la aplicación de Azure AD relacionada.


## Office 365 ProPlus

| Nombre del atributo| Usuario| Comentario |
| --- | :-: | --- |
| accountEnabled| X| Define si se habilita una cuenta.|
| cn| X| |
| DisplayName| X| |
| objectSID| X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| pwdLastSet| X| Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos. Usada por sincronización de contraseñas y federación.|
| sourceAnchor| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| usageLocation| X| Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|


## Exchange Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| assistant| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| Este atributo no se consume actualmente para grupos.|
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| Este atributo no se consume actualmente por Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Este atributo no se consume actualmente por Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Este atributo no se consume actualmente por Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Este atributo no se consume actualmente por Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Este atributo no se consume actualmente por Exchange Online. |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos. Usada por sincronización de contraseñas y federación.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userCertificate| X| X| | |
| userPrincipalName| X| | | UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |



## SharePoint Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos. Usada por sincronización de contraseñas y federación.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| | | UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|
| wWWHomePage| X| X| | |

## Lync Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos. Usada por sincronización de contraseñas y federación.|
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| título| X| X| | |
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| | | UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|
| wWWHomePage| X| X| | |


## Azure RMS

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| cn| X| | X| Nombre común o alias. Frecuentemente el prefijo del valor de [mail].|
| DisplayName| X| X| X| Una cadena que representa el nombre que se muestra a menudo como el nombre descriptivo (nombre, apellido).|
| mail| X| X| X| Dirección de correo electrónico completa.|
| member| | | X| |
| objectSID| X| | X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| proxyAddresses| X| X| X| Propiedad mecánica. Usado por Azure AD. Contiene todas las direcciones de correo electrónico secundarias para el usuario.|
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos.|
| securityEnabled| | | X| Deriva de groupType.|
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| | | Este UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|


## Intune

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| DisplayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos. Usada por sincronización de contraseñas y federación.|
| securityEnabled| | | X| Deriva de groupType|
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| | | UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|



## Dynamics CRM

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| DisplayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos. Usada por sincronización de contraseñas y federación.|
| securityEnabled| | | X| Deriva de groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| título| X| X| | |
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| | | UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|

## Aplicaciones de terceros
Se trata de un conjunto de atributos que se pueden usar si no se usa el directorio de Azure AD para la compatibilidad con Office 365, Dynamics o Intune. Tiene un pequeño conjunto de atributos principales.

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | Define si se habilita una cuenta.|
| cn| X| | X| |
| DisplayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | Propiedad mecánica. Identificador de usuario de AD usado para mantener la sincronización entre Azure AD y AD.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Propiedad mecánica. Usada para saber cuándo invalidar tokens ya emitidos. Usada por sincronización de contraseñas y federación.|
| sn| X| X| | |
| sourceAnchor| X| X| X| Propiedad mecánica. Identificador inmutable para mantener la relación entre ADDS y Azure AD.|
| usageLocation| X| | | Propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| | | UPN es el identificador de inicio de sesión para el usuario. Frecuentemente el mismo que el valor [mail].|

## Windows 10
Los equipos (dispositivos) unidos a dominio de Windows 10 sincronizarán algunos atributos con Azure AD. Para más información sobre los escenarios, vea [Conexión de dispositivos unidos a un dominio a Azure AD para experiencias en Windows 10](active-directory-azureadjoin-devices-group-policy.md). Estos atributos siempre se sincronizarán y Windows 10 no aparecerá como una aplicación de la que puede anular la selección. Un equipo unido a un dominio de Windows 10 se identifica por tener el atributo userCertificate rellenado.

| Nombre del atributo| Dispositivo| Comentario |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valor codificado de forma rígida para equipos unidos al dominio. |
| DisplayName | X| |
| ms-DS-CreatorSID | X| También se denomina registeredOwnerReference.|
| objectGUID | X| También se denomina deviceID.|
| objectSID | X| También se denomina onPremisesSecurityIdentifier.|
| operatingSystem | X| También se denomina deviceOSType.|
| operatingSystemVersion | X| También se denomina deviceOSVersion.|
| userCertificate | X| |

Estos atributos para usuario se incluyen además de las otras aplicaciones que haya seleccionado.

| Nombre del atributo| Usuario| Comentario |
| --- | :-: | --- |
| domainFQDN| X| También se denomina dnsDomainName. Por ejemplo, contoso.com.|
| domainNetBios| X| También se denomina netBiosName. Por ejemplo, CONTOSO.|

## Reescritura híbrida de Exchange
Estos atributos se reescriben desde Azure AD en Active Directory local cuando se elige habilitar la implementación híbrida de Exchange. Dependiendo de la versión de Exchange, puede que se sincronicen menos atributos.

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X| | | Se deriva de cloudAnchor en Azure AD. Esto es nuevo en Exchange 2016.|
| msExchArchiveStatus| X| | | Archivo en línea: permite a los clientes archivar el correo electrónico.|
| msExchBlockedSendersHash| X| | | Filtrado: reescribe los datos de remitentes seguros y bloqueados en línea y el filtrado de local de los clientes.|
| msExchSafeRecipientsHash| X| | | Filtrado: reescribe los datos de remitentes seguros y bloqueados en línea y el filtrado de local de los clientes.|
| msExchSafeSendersHash| X| | | Filtrado: reescribe los datos de remitentes seguros y bloqueados en línea y el filtrado de local de los clientes.|
| msExchUCVoiceMailSettings| X| | | Habilitar mensajería unificada (UM) - correo de voz en línea: usado para la integración de Microsoft Lync Server para indicar a Lync Server local que el usuario tiene el correo de voz en los servicios en línea.|
| msExchUserHoldPolicies| X| | | Retención por juicio: permite que los servicios en la nube determinen qué usuarios están bajo retención por juicio.|
| proxyAddresses| X| X| X| Solo se inserta la dirección x500 de Exchange Online.|

## Notas sobre los atributos
- Cuando se usa un identificador alternativo, el atributo local userPrincipalName se sincronizará con el atributo de Azure AD onPremisesUserPrincipalName. El atributo Alternate ID, por ejemplo, el correo, se sincronizará con el atributo de Azure AD userPrincipalName.


## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->