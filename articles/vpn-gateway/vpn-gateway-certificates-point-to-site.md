<properties 
   pageTitle="Creación de certificados autofirmados para la configuración entre locales de la puerta de enlace de VPN de punto a sitio con makecert | Microsoft Azure"
   description="Este artículo muestra cuáles son los pasos para crear certificados raíz autofirmados mediante makecert en Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/15/2016"
   ms.author="cherylmc" />

# Trabajar con certificados raíz autofirmados para las configuraciones de punto a sitio

Las tareas siguientes le ayudarán a crear un certificado raíz mediante makecert y a generar certificados de cliente desde el certificado raíz. Los siguientes pasos están destinados al uso de makecert en Windows 10.

## Crear un certificado raíz autofirmado

Makecert es una forma de crear un certificado raíz autofirmado. Los siguientes pasos le guiarán por la creación de un certificado raíz autofirmado mediante makecert. Estos pasos no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

### Para crear un certificado raíz autofirmado

1. Desde un equipo que ejecute Windows 10, descargue e instale el [Kit de desarrollo de software de Windows (SDK) para Windows 10](https://dev.windows.com/es-ES/downloads/windows-10-sdk).

2. Después de la instalación, la utilidad makecert.exe se encuentra en: C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>.
		
	Ejemplo:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. A continuación, cree e instale un certificado raíz en el almacén de certificados personal de su equipo. En el ejemplo que aparece a continuación, se crea un archivo *.cer* correspondiente que cargará más adelante. Ejecute como administrador el siguiente comando, donde *RootCertificateName* es el nombre que desea usar para el certificado.

	Nota: si ejecuta el ejemplo siguiente sin realizar ningún cambio, el resultado será un certificado raíz y el archivo *RootCertificateName.cer* correspondiente. Puede encontrar el archivo .cer en el directorio desde donde ejecutó el comando. El certificado estará en sus Certificados, en Usuario actual\\Personal\\Certificados.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Puesto que ha creado un certificado raíz desde el que se generarán los certificados de cliente, puede que desee exportar este certificado junto con su clave privada y guardarlo en una ubicación segura donde se pueda recuperar.

## Generar, exportar e instalar certificados de cliente

Puede generar un certificado de cliente desde un certificado raíz autofirmado y, luego, exportar e instalar el certificado de cliente. Los pasos siguientes no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

### Generar un certificado de cliente desde un certificado raíz autofirmado.

Los pasos siguientes le guiarán por una forma de generar un certificado de cliente desde un certificado raíz autofirmado. Puede generar varios certificados de cliente desde el mismo certificado raíz. Luego, cada certificado de cliente se puede exportar e instalar en el equipo cliente.

1. En el mismo equipo que usó para crear el certificado raíz autofirmado, abra un símbolo del sistema como administrador.

2. Cambie el directorio a la ubicación donde desea guardar el archivo de certificado de cliente. *RootCertificateName* hace referencia al certificado raíz autofirmado que ha generado. Si ejecuta el ejemplo siguiente (cambiando el NombreCertificadoRaíz por el nombre de su certificado raíz), el resultado será un certificado de cliente denominado "NombreCertificadoCliente" en el almacén de certificados personales.

3. Escriba el siguiente comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos los certificados se almacenan en su almacén de certificados, en Usuario actual\\Personal\\Certificados, de su equipo. Puede generar tantos certificados de cliente como sean necesarios según este procedimiento.

### Exportar un certificado de cliente

1. Para exportar un certificado de cliente, use *certmgr.msc*. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**. Se abrirá el asistente para la exportación de certificados.

2. En el asistente, haga clic en **Siguiente**, seleccione **Sí, exportar la clave privada** y, luego, haga clic en **Siguiente**.

3. En la página **Formato de archivo de exportación**, puede dejar seleccionados los valores predeterminados. A continuación, haga clic en **Siguiente**.
 
4. En la página **Seguridad**, debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. Luego, haga clic en **Siguiente**.

5. En **Archivo que se va a exportar**, **vaya** a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.

6. Haga clic en **Finalizar** para exportar el certificado.

### Instalar un certificado de cliente

Cada cliente que quiera conectar a la red virtual mediante una conexión de punto a sitio debe tener instalado un certificado de cliente. Este certificado es adicional al paquete de configuración de VPN necesario. Los pasos siguientes le guiarán por la instalación manual del certificado de cliente.

1. Ubique el archivo *.pfx* y cópielo en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Deje la **Ubicación del almacén** como **Usuario actual** y, luego, haga clic en **Siguiente**.

2. En la página **Archivo** que se va a importar, no haga ningún cambio. Haga clic en **Siguiente**.

3. En la página **Protección de clave privada**, ingrese la contraseña del certificado, si usó una, o compruebe que la entidad de seguridad que instala el certificado es correcta y, luego, haga clic en **Siguiente**.

4. En la página **Almacén de certificados**, deje la ubicación predeterminada y, a continuación, haga clic en **Siguiente**.

5. Haga clic en **Finalizar** En la **Advertencia de seguridad** para la instalación de certificados, haga clic en **Sí**. El certificado se importó correctamente.

## Pasos siguientes

Continúe con la configuración de punto a sitio.

- Para ver los pasos del modelo de implementación del **Administrador de recursos**, consulte [Configuración de una conexión punto a sitio a una red virtual mediante PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Para ver los pasos del modelo de implementación **clásica**, consulte [Configuración de una conexión VPN de punto a sitio a una red virtual](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0121_2016-->