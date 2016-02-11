<properties
   pageTitle="Solución de problemas de la extensión del Panel de acceso para Internet Explorer | Microsoft Azure"
   description="Cómo usar la directiva de grupo para implementar el complemento de Internet Explorer para el portal de Mis aplicaciones."
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/28/2015"
   ms.author="liviodlc"/>

#Solución de problemas de la extensión del Panel de acceso para Internet Explorer

Este artículo le ayudará a solucionar los siguientes problemas:

- No puede tener acceso a sus aplicaciones a través del portal de Mis aplicaciones con Internet Explorer.
- Verá el mensaje "Instalar software" aunque ya haya instalado el software.

Si es administrador, vea también: [Cómo implementar la extensión del Panel de acceso para Internet Explorer con la Directiva de grupo](active-directory-saas-ie-group-policy.md).

##Ejecutar la herramienta de diagnóstico

Puede diagnosticar problemas de instalación con la extensión del Panel de acceso descargando y ejecutando la herramienta de diagnóstico del Panel de acceso:

1. [Haga clic aquí para descargar la herramienta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Abra el archivo y presione el botón **Extraer todo**.

	![Presionar Extraer todo](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. A continuación, presione el botón **Extraer** para continuar.

	![Presionar Extraer](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Para ejecutar la herramienta, haga clic con el botón secundario en el archivo denominado **AccessPanelExtensionDiagnosticTool** y luego seleccione **Abrir con > Host de script basado en Microsoft Windows**.

	![Abrir con > Host de script basado en Microsoft Windows](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. A continuación verá la siguiente ventana de diagnóstico, en la que se describe qué puede haber incorrecto con la instalación.

	![Un ejemplo de la ventana de diagnóstico](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Haga clic en "**SÍ**" para permitir que el programa corrija los problemas que se han encontrado.

7. Para guardar estos cambios, cierre cada ventana de Internet Explorer y luego vuelva a abrir Internet Explorer.<br />Si todavía no puede tener acceso a sus aplicaciones, pruebe los pasos siguientes.

##Comprobar que la extensión del Panel de acceso está activada

Para comprobar que la extensión del Panel de acceso está habilitada en Internet Explorer:

1. En Internet Explorer, haga clic en el **icono de engranaje** de la esquina superior derecha de la ventana. Luego seleccione **Opciones de Internet**.<br />(En versiones anteriores de Internet Explorer puede encontrar esta opción en **Herramientas > Opciones de Internet**.

	![Ir a Herramientas > Opciones de Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Haga clic en la pestaña **Programas** y luego en el botón **Administrar complementos**.

	![Hacer clic en Administrar complementos](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. En este cuadro de diálogo, seleccione **Extensión del Panel de acceso** y luego haga clic en el botón **Habilitar**.

	![Hacer clic en Habilitar](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Para guardar estos cambios, cierre cada ventana de Internet Explorer y luego vuelva a abrir Internet Explorer.

##Habilitar extensiones para la exploración de InPrivate

Si usa el modo de exploración de InPrivate:

1. En Internet Explorer, haga clic en el **icono de engranaje** de la esquina superior derecha de la ventana. Luego seleccione **Opciones de Internet**.<br />(En versiones anteriores de Internet Explorer puede encontrar esta opción en **Herramientas > Opciones de Internet**.

	![Un ejemplo de la ventana de diagnóstico](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Vaya a la pestaña **Privacidad** y **desactive** la casilla **Deshabilitar barras de herramientas y extensiones cuando se inicie la exploración de InPrivate**</p>.

	![Desactivar Deshabilitar barras de herramientas y extensiones cuando se inicie la exploración de InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Para guardar estos cambios, cierre cada ventana de Internet Explorer y luego vuelva a abrir Internet Explorer.

##Desinstalar la extensión del Panel de acceso

Para desinstalar la extensión del Panel de acceso desde el equipo:

1. En el teclado, presione la **clave de Windows** para abrir el menú Inicio. Cuando se abre el menú, puede escribir cualquier cosa para realizar una búsqueda. Escriba "Panel de Control" y luego abra el **Panel de Control** cuando aparezca en los resultados de la búsqueda.

	![Buscar el Panel de Control](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. En la esquina superior derecha del Panel de Control, cambie la opción **Ver** a **Iconos grandes**. Luego busque y haga clic en el botón **Programas y características**.

	![Cambiar la vista para mostrar iconos grandes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. En la lista, seleccione **Extensión del Panel de acceso** y luego haga clic en el botón **Desinstalar**.

	![Hacer clic en Desinstalar](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. Después, puede intentar instalar la extensión de nuevo para ver si se ha resuelto el problema.

Si encuentra problemas al desinstalar la extensión, también puede quitarla usando la herramienta [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673).

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=Oct15_HO3-->