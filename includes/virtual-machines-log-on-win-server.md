<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Al hacer clic en **Conectar** se crea y descarga un archivo de Protocolo de escritorio remoto (archivo .rdp). Haga clic en **Abrir** para utilizar este archivo.

5. En la ventana de Escritorio remoto, haga clic en **Conectar** para continuar.

	![Continuar con la conexión](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. En la ventana de Seguridad de Windows, escriba las credenciales de la cuenta en la máquina virtual y luego haga clic en **Aceptar**.

 	En la mayoría de los casos, las credenciales son el nombre de usuario y la contraseña de la cuenta local que especificó al crear la máquina virtual. En este caso, el dominio es el nombre de la máquina virtual y se escribe como *nombredevm*&#92;*nombredeusuario*.
	
	Si la máquina virtual pertenece a un dominio de su organización, asegúrese de que el nombre de usuario incluye el nombre de ese dominio con el formato *Dominio*&#92;*Nombredeusuario*. La cuenta también deberá estar en el grupo de administradores o tener privilegios de acceso remoto a la VM.
	
	Si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.

7.	Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar el inicio de sesión.

	![Verificar la identidad de la máquina](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=AcomDC_0128_2016-->