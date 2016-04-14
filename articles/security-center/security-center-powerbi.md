<properties
   pageTitle="Obtención de información mediante los datos de Azure Security Center con Power BI| Microsoft Azure"
   description="El paquete de contenido de Power BI para Azure Security Center facilita la búsqueda de alertas de seguridad, recomendaciones, recursos atacados y tendencias en función de un conjunto de datos que se ha creado para los informes."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/25/2016"
   ms.author="yurid"/>

# Obtención de información mediante los datos de Azure Security Center con Power BI
El [panel de Power BI](http://aka.ms/azure-security-center-power-bi) para Azure Security Center le permite visualizar, analizar y filtrar las recomendaciones y alertas de seguridad desde cualquier lugar, incluido su dispositivo móvil. Utilice el panel de Power BI para mostrar tendencias y patrones de ataque. Consulte alertas de seguridad por recursos o dirección IP de origen y riesgos de seguridad no solucionados por recursos o antigüedad. Puede también recopilar recomendaciones y alertas de seguridad de Security Center junto con otros datos de diversas maneras, por ejemplo con los [registros de auditoría de Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) y la [auditoría de Base de datos SQL de Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/), ya que ambos ofrecen paneles de Power BI, o puede exportar estos datos a Excel para obtener informes fácilmente sobre el estado de seguridad de los recursos en la nube.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## Exploración de los datos de Azure Security Center con los servicios de Power BI
Conéctese al [paquete de contenido de Azure Security Center](https://app.powerbi.com/groups/me/getdata/services/azure-security-center) en Power BI y siga los pasos siguientes:

1\. Haga clic en **Conectar** en el icono de Azure Security Center para continuar.

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig1.png)

2\. Se abre la ventana **Conectar con Azure Security Center**. En el campo **Identificador de suscripción de Azure**, escriba la suscripción de Azure y haga clic en **Siguiente**.

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig2.png)

3\. En la lista desplegable **Método de autenticación**, seleccione **oAuth2** y haga clic en **Iniciar sesión**.

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig3.png)

4\. Se le redirigirá a una página de autenticación donde debe escribir las credenciales que usa para conectarse a Azure Security Center. Una vez finalizado el proceso de autenticación, Power BI iniciará la importación de datos para generar los informes. Durante este tiempo puede ver el mensaje siguiente en la esquina derecha del explorador:

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

5\. Una vez finalizado el proceso, se cargará el panel de Power BI para Azure Security Center con los informes tal como se muestra a continuación:

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig5.png)

De un vistazo puede ver el número de alertas de seguridad y recomendaciones, así como el número de máquinas virtuales, Bases de datos SQL de Azure y recursos de red supervisados por Azure Security Center.

Un vínculo a Azure Security Center le redirigirá al Portal de Azure. Los gráficos facilitan la visualización de la información acerca de las recomendaciones y alertas de seguridad, incluidas:

- Estado de seguridad de los recursos
- Información general de recomendaciones pendientes
- Recomendaciones de máquina virtual
- Alertas a lo largo del tiempo
- Recursos atacados
- Direcciones IP atacadas

Detrás de cada gráfico hay información adicional. Seleccione un icono para ver más información, por ejemplo el icono de estado de seguridad de los recursos muestra detalles adicionales sobre recomendaciones pendientes por recursos tal como se muestra a continuación:

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Si hace clic en cualquier línea de este gráfico, las demás se atenuarán con lo que se podrá centrar en la que ha seleccionado. Para volver al panel, haga clic en **Azure Security Center** en la opción **Paneles** del panel izquierdo de esta página.

> [AZURE.NOTE] Si desea personalizar los informes, mediante la incorporación de campos adicionales o el cambio de elementos visuales existentes podrá editar el informe. Lea el artículo [Interacción con un informe en la vista de edición en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) para más información.

Los iconos para **Alertas a lo largo del tiempo**, **Recursos atacados** y **Direcciones IP atacadas** proporcionarán un resultado similar al hacer clic en cada uno de ellos. Esto se debe a que el informe agrega información acerca de las tres variables y la llama **Recursos atacados** tal como se muestra a continuación:

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig7.png)

En este momento puede también guardar una copia de este informe, imprimirlo o publicarlo en la web mediante el uso de las opciones disponibles en el menú **Archivo**.

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig8.png)


##Utilización del panel de Azure Security Center para acceder a Power BI
También puede utilizar el panel de Azure Security Center para tener acceso a informes de Power BI. Siga estos pasos para realizar esta tarea:

1\. En el panel de **Azure Security Center** haga clic en el botón **Explorar en Power BI**.

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig9.png)

2\. La hoja **Explorar en Power BI** se abre en el lado derecho como se muestra a continuación:

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig10-1.png)

3\. En la lista desplegable **Elegir una suscripción para explorar en Power BI**, seleccione la suscripción que desea utilizar.

4\. En el campo **Copiar el identificador de suscripción**, haga clic en el botón Copiar. 5. Haga clic en el botón **Ir a Power BI**. 6. Se abre la ventana **Conectar con Azure Security Center**. En el campo **Identificador de suscripción de Azure**, escriba la suscripción de Azure y haga clic en **Siguiente**.

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig2.png)

7\. En la lista desplegable **Método de autenticación**, seleccione **oAuth2** y haga clic en **Iniciar sesión**.

![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig3.png)

8\. Se le redirigirá a una página de autenticación donde debe escribir las credenciales que usa para conectarse a Azure Security Center. Una vez finalizado el proceso de autenticación, Power BI iniciará la importación de datos para generar los informes.

## Pasos siguientes
En este documento, ha aprendido a usar Power BI en Azure Security Center. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información sobre cómo configurar los ajustes de seguridad en el Centro de seguridad de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): Encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0302_2016-->