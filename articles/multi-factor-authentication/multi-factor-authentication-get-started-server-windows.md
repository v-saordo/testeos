<properties 
	pageTitle="Servidor de Autenticación de Windows y Azure Multi-Factor Authentication" 
	description="Se trata de la página Azure Multi-Factor Authentication que ayudará a implementar la Autenticación de Windows y el servidor de Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Servidor de Autenticación de Windows y Azure Multi-Factor Authentication

La sección Autenticación de Windows permite al administrador habilitar y configurar la autenticación de Windows para una o más aplicaciones. A continuación se facilita una lista de cosas que deben tenerse en cuenta antes de configurar la Autenticación de Windows.

-  Es necesario reiniciar el equipo antes de que la Azure Multi-Factor Authentication para que Terminal Services entre en vigor.
-  Si se encuentra marcada la opción “Requerir coincidencia de usuario de Azure Multi-Factor Authentication” y usted no está en la lista de usuarios, no podrá iniciar sesión en el equipo después del reinicio.
-  Las direcciones IP de confianza dependen de si la aplicación puede proporcionar la dirección IP de cliente con la autenticación. Actualmente solo se admite Terminal Services.  







>[AZURE.NOTE]Esta característica no se admite para proteger Terminal Services en Windows Server 2012 R2.
 



## Para proteger una aplicación con la Autenticación de Windows, use el procedimiento siguiente.

1. En el servidor de Azure Multi-Factor Authentication, haga clic en el icono de Autenticación de Windows.![Autenticación de Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Marque la casilla de verificación de Activar autenticación de Windows. De forma predeterminada, esta casilla está desmarcada.
3. La pestaña Aplicaciones permite al administrador configurar una o más aplicaciones para la Autenticación de Windows.
4. Seleccione un servidor o una aplicación (especifique si el servidor o la aplicación está habilitado). Haga clic en Aceptar.
5. Haga clic en el botón Agregar...
6. La pestaña IP fiables permite omitir Azure Multi-Factor Authentication para sesiones de Windows procedentes de direcciones IP específicas. Por ejemplo, si los empleados usan la aplicación desde la oficina y desde casa, puede decidir que no desea que sus teléfonos suenen para la Azure Multi-Factor Authentication mientras se encuentra en la oficina. Para ello, debe especificar la subred de la oficina como entrada de IP de confianza.
7. Haga clic en el botón Agregar...
8. Si desea omitir una única dirección IP, seleccione IP única.
9. Seleccione Rango de direcciones IP si desea omitir un intervalo IP completo. Ejemplo 10.63.193.1-10.63.193.100.
10. Seleccione Subred si desea especificar un intervalo de direcciones IP mediante la notación de subred. Escriba la dirección IP inicial de la subred y seleccione la máscara de red adecuada de la lista desplegable. 
11. Haga clic en el botón Aceptar.

<!---HONumber=AcomDC_0224_2016-->