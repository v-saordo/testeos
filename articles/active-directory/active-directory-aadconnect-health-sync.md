
<properties
	pageTitle="Uso de Azure AD Connect Health con sincronización | Microsoft Azure"
	description="Esta es la página de Azure AD Connect Health donde se describe cómo supervisar la sincronización de Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/17/2016"
	ms.author="billmath"/>

# Uso de Azure AD Connect Health para sincronización
La siguiente documentación es específica de la supervisión de sincronización de Azure AD Connect con Azure AD Connect Health. Para obtener información sobre la supervisión de AD FS con Azure AD Connect Health, consulte [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md).

![Azure AD Connect Health para sincronización](./media/active-directory-aadconnect-health-sync/sync.png)

## Alertas de Azure AD Connect Health para sincronización
La sección Alertas de Azure AD Connect Health para sincronización proporciona la lista de alertas activas. Cada alerta incluye información pertinente, pasos de resolución y vínculos a documentación relacionada. Al seleccionar una alerta activa o una alerta resulta, verá una hoja nueva con información adicional, así como los pasos que puede seguir para resolver la alerta y vínculos a documentación adicional. También puede ver datos históricos sobre las alertas resueltas en el pasado.

Al seleccionar una alerta, recibirá información adicional, así como los pasos que puede seguir para resolver la alerta y vínculos a documentación adicional.

![Error de sincronización de Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

## Recomendación de sincronización
Con la última versión de Azure AD Connect Health para la sincronización se han agregado las siguientes capacidades nuevas:

- Latencia de operaciones de sincronización
- Tendencia de cambio de objeto

### Latencia de sincronización
Esta característica proporciona una tendencia gráfica de la latencia de las operaciones de sincronización (importación, exportación, etc.) para los conectores. Esto proporciona una forma rápida y fácil de entender no solo la latencia de las operaciones (grande si se produce un gran conjunto de cambios), sino también una manera de detectar anomalías en la latencia que pueden requerir más investigación.

![Latencia de sincronización](./media/active-directory-aadconnect-health-sync/synclatency.png)

De manera predeterminada, solo se muestra la latencia de la operación de exportación del conector de Azure AD. Para ver más operaciones del conector o para ver las operaciones de otros conectores, haga clic con el botón derecho en el gráfico y elija la operación específica y el conector.

### Cambios en el objeto de sincronización
Esta característica proporciona una tendencia gráfica del número de cambios que se evalúan y exportan a Azure AD. Hoy en día, es difícil intentar recopilar esta información de los registros de sincronización. El gráfico le proporciona, no solo una manera más sencilla de supervisar el número de cambios que se producen en su entorno, sino también una vista de los errores que ocurren.

![Latencia de sincronización](./media/active-directory-aadconnect-health-sync/syncobjectchanges.png)

## Vínculos relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0224_2016-->