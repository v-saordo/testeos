
En este artículo se muestra cómo configurar HTTPS para una aplicación web en el Servicio de aplicaciones de Azure. No se cubre la autenticación de certificado de cliente; para obtener información al respecto, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](../articles/app-service-web/app-service-web-configure-tls-mutual-auth.md).

De forma predeterminada, Azure ya habilita HTTPS para la aplicación con un certificado comodín para el dominio *.azurewebsites.net. Si no tiene pensado configurar un dominio personalizado, puede aprovechar el certificado HTTPS predeterminado. Sin embargo, al igual que [todos los dominios comodín](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), no es tan seguro como usar un dominio personalizado con su propio certificado

En el resto del documento se proporcionan detalles acerca de cómo habilitar HTTPS para dominios personalizados, como **contoso.com**, **www.contoso.com** o ***.contoso.com**

<a name="bkmk_domainname"></a>
## Habilitación de SSL en un dominio personalizado

Para habilitar HTTPS para un dominio personalizado, como **contoso.com**, primero debe [configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](../articles/app-service-web/web-sites-custom-domain-name.md). Después, haga lo siguiente:

1. [Obtener un certificado SSL](#bkmk_getcert)
2. [Configurar el plan de tarifa Estándar o Premium](#bkmk_standardmode)
2. [Configurar SSL en la aplicación](#bkmk_configuressl)
3. [Implementar SSL en la aplicación](#bkmk_enforce) (opcional)

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure y de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.

<a name="bkmk_getcert"></a>
## 1\. Obtener un certificado SSL

Antes de solicitar un certificado SSL, debe determinar los nombres de dominio que contarán con la protección del certificado. De este modo, se determinará el tipo de certificado que debe obtener. Si necesita proteger un solo nombre de dominio, como **contoso.com** o **www.contoso.com**, será suficiente con un certificado básico. Si necesita proteger varios nombres de dominio, como **contoso.com**, **www.contoso.com** y **mail.contoso.com**, puede obtener un [certificado comodín](http://en.wikipedia.org/wiki/Wildcard_certificate) o un certificado con [nombre alternativo de sujeto](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName).

Los certificados SSL que se usan con el Servicio de aplicaciones debe firmarlos una [entidad de certificación](http://en.wikipedia.org/wiki/Certificate_authority). Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que expida certificados SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Programa de certificados raíz de SSL para Windows y Windows Phone 8 (Entidades de certificación miembros)) en Microsoft TechNet Wiki.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

* El certificado debe contener una clave privada.
* El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
* El nombre de sujeto del certificado debe coincidir con el del dominio usado para acceder a la aplicación. Si necesita ofrecer servicio a varios dominios con este certificado, deberá usar un valor comodín o especificar los valores subjectAltName tal y como se ha descrito previamente.
* Este certificado debe usar un cifrado de 2048 bits como mínimo.
* Los certificados emitidos por servidores de entidades de certificación privados no son compatibles con el Servicio de aplicaciones de Azure.

Para obtener un certificado SSL para utilizarlo con el Servicio de aplicaciones de Azure, envíe una solicitud de firma de certificado (CSR) a una entidad de certificación y, a continuación, genere un archivo .pfx a partir del certificado que se recibe. Puede hacerlo mediante la herramienta de su elección. A continuación se muestran algunas de las formas comunes de obtener un certificado:

- [Obtención de un certificado con Certreq.exe](#bkmk_certreq)
- [Obtención de un certificado con el Administrador de IIS](#bkmk_iismgr)
- [Obtención de un certificado con OpenSSL](#bkmk_openssl)
- [Obtención de un certificado SubjectAltName con OpenSSL](#bkmk_subjectaltname)
- [Generación de certificados autofirmados (solo para pruebas)](#bkmk_selfsigned)

> [AZURE.NOTE] Al seguir los pasos, se le solicitará que escriba un **nombre común**, como `www.contoso.com`. Para los certificados comodín, este valor debe ser *.nombreDeDominio (por ejemplo, *.contoso.com). Si necesita compatibilidad con un nombre comodín, como *.contoso.com, y con un nombre de dominio raíz, como contoso.com, puede usar un certificado comodín subjectAltName.
>
> Los certificados ECC (criptografía de curva elíptica) son compatibles con el Servicio de aplicaciones de Azure; sin embargo, son relativamente nuevos, por lo que es preciso preguntar a la entidad de certificación los pasos exactos para crear el CSR.

Es posible que también tenga que obtener **[certificados intermedios](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (conocidos también como certificados de cadena), en caso de que la entidad de certificación los use. Se considera que el uso de certificados intermedios es más seguro que los "certificados que no pertenecen a una cadena", por lo que las entidades de certificación suelen utilizarlos. Normalmente, los certificados intermedios se pueden descargar de forma independiente desde el sitio web de la entidad de certificación. En este artículo se proporcionan los pasos necesarios para asegurarse de que los certificados intermedios se combinen con el certificado cargado en sus aplicaciones.

<a name="bkmk_certreq"></a>
### Obtención de un certificado con Certreq.exe (solo Windows)

Certreq.exe es una utilidad de Windows para crear solicitudes de certificado. Ha formado parte de la instalación base de Windows desde Windows XP/Windows Server 2000, por lo que debe estar disponible en los sistemas Windows más recientes. Realice los siguientes pasos para obtener un certificado SSL con certreq.exe.

1. Abra **Bloc de notas** y cree un documento nuevo que contenga lo siguiente. En la línea del asunto, sustituya **mysite.com** por el nombre de dominio personalizado de la aplicación. Por ejemplo, Subject = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Para obtener más información sobre las opciones especificadas anteriormente, así como otras opciones disponibles, consulte la [documentación de referencia de Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Guarde el archivo de texto como **myrequest.txt**.

3. En la pantalla **Inicio** o en el **menú Inicio**, ejecute **cmd.exe**.

4. Desde el símbolo del sistema, use el siguiente comando para crear un archivo de solicitud de certificado:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Especifique la ruta de acceso al archivo **myrequest.txt** creado en el paso 1 y la ruta de acceso que se va a usar al crear el archivo **myrequest.csr**.

5. Envíe **myrequest.csr** a una entidad de certificación para obtener un certificado SSL. Puede que sea necesario cargar el archivo o abrirlo en la aplicación Bloc de notas y pegar los contenidos directamente en el formulario web.

	Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Programa de certificados raíz de SSL para Windows y Windows Phone 8 (Entidades de certificación miembros)) en Microsoft TechNet Wiki.

6. Una vez que la entidad de certificación le haya proporcionado un archivo de certificado (.CER), guarde este archivo en el equipo usado para generar la solicitud y, a continuación, use el siguiente comando para aceptar la solicitud y completar el proceso de generación del certificado.

		certreq -accept -user mycert.cer

	En este caso, el certificado **mycert.cer** recibido de la entidad de certificación se usará para completar la firma del certificado. No se creará ningún archivo; en su lugar, el certificado se almacenará en el almacén de certificados de Windows.

6. Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión proporcionada para Microsoft IIS.

	Una vez que haya descargado el certificado, haga clic en él con el botón derecho en el explorador y seleccione **Instalar certificado**. Use los valores predeterminados del **Asistente para importación de certificados** y seleccione repetidamente **Siguiente** hasta que la importación haya finalizado.

7. Para exportar el certificado desde el almacén de certificados, ejecute **certmgr.msc** en la pantalla **Inicio** o en el **menú Inicio**. Cuando aparezca el **administrador de certificados**, expanda la carpeta **Personal** y seleccione **Certificados**. En el campo **Emitido para**, busque una entrada con el nombre de dominio personalizado para el que solicitó un certificado. En el campo **Emitido por**, debe aparecer la entidad de certificación que usó para este certificado.

	![Insertar imagen del administrador de certificados aquí][certmgr]

9. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** y, a continuación, seleccione **Exportar**. En el **Asistente para exportar certificados**, haga clic en **Siguiente** y, a continuación, seleccione **Exportar la clave privada**. Haga clic en **Siguiente**.

	![Exportar la clave privada][certwiz1]

10. Seleccione **Intercambio de información personal - PKCS #12**, **Incluir todos los certificados en la cadena de certificados** y **Exportar todas las propiedades extendidas**. Haga clic en **Siguiente**.

	![incluir todos los certificados y propiedades extendidas][certwiz2]

11. Seleccione **Contraseña** y, a continuación, escriba y confirme la contraseña. Haga clic en **Siguiente**.

	![especificar una contraseña][certwiz3]

12. Proporcione una ruta y el nombre del archivo que contendrá el certificado exportado. El nombre de archivo debe tener la extensión **.pfx**. Haga clic en **Siguiente** para finalizar el proceso.

	![proporcionar una ruta de archivo][certwiz4]

Ya puede cargar el archivo PFX exportado en el Servicio de aplicaciones de Azure.

<a name="bkmk_openssl"></a>
### Obtención de un certificado con OpenSSL

1. Genere una clave privada y una solicitud de firma de certificado utilizando lo siguiente desde una línea de comandos, una sesión Bash o una sesión de Terminal.

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Cuando se le solicite, escriba la información apropiada. Por ejemplo:

 		Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []:

	Al finalizar el proceso, debería tener dos archivos: **myserver.key** y **server.csr**. El archivo **server.csr** contiene la solicitud de firma de certificado.

3. Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Programa de certificados raíz de SSL para Windows y Windows Phone 8 (Entidades de certificación miembros)) en Microsoft TechNet Wiki.

4. Una vez que haya obtenido un certificado de una entidad de certificación, guárdelo en un archivo llamado **myserver.crt**. Si la entidad de certificación ha proporcionado el certificado en formato de texto, solo tiene que pegar el texto del certificado en el archivo **myserver.crt**. El contenido del archivo debería parecerse a lo siguiente, al visualizarlo en un editor de texto:

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	Guarde el archivo .

5. En la línea de comandos, una sesión de Bash o una sesión de Terminal, use el siguiente comando para convertir **myserver.key** y **myserver.crt** en **myserver.pfx**, que es el formato que necesita el Servicio de aplicaciones de Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	> [AZURE.NOTE] Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión que se le ha proporcionado como archivo PEM (extensión de archivo .pem).
	>
	> El siguiente comando muestra cómo se crea un archivo .pfx que incluya certificados intermedios, que se encuentra en contiene el archivo **intermediate-cets.pem**:
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Después de ejecutar este comando, debería tener el archivo **myserver.pfx**, que es apropiado para usarlo con el Servicio de aplicaciones de Azure.

<a name="bkmk_iismgr"></a>
### Obtención de un certificado con el Administrador de IIS

Si está familiarizado con el Administrador de IIS, puede usarlo para generar un certificado que se pueda utilizar con el Servicio de aplicaciones de Azure.

1. Genere una CSR con el Administrador de IIS para enviarlo a la entidad de certificación. Para obtener más información acerca de la generación de solicitudes CSR, consulte [Solicitar un certificado de servidor de Internet (IIS 7)][iiscsr].

2. Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Programa de certificados raíz de SSL para Windows y Windows Phone 8 (Entidades de certificación miembros)) en Microsoft TechNet Wiki.

3. Complete la solicitud CSR con el certificado proporcionado por la entidad de certificación. Para obtener más información acerca de la realización de solicitudes CSR, consulte [Instalación de un certificado de servidor de Internet (IIS 7)][installcertiis].

4. Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión proporcionada para Microsoft IIS.

	Una vez que haya descargado el certificado, haga clic en él con el botón derecho en el explorador y seleccione **Instalar certificado**. Use los valores predeterminados del **Asistente para importación de certificados** y seleccione repetidamente **Siguiente** hasta que la importación haya finalizado.

4. Exporte el certificado desde el Administrador de IIS. Para obtener más información acerca de la exportación del certificado, consulte [Exportación de un certificado de servidor (IIS 7)][exportcertiis]. El archivo exportado se cargará en Azure en los pasos posteriores para usarlo en su aplicación.

	> [AZURE.NOTE] Durante el proceso de exportación, asegúrese de seleccionar la opción <strong>Exportar la clave privada</strong>. Dicha exportación incluye la clave privada en el certificado exportado.

	> [AZURE.NOTE] Durante el proceso de exportación, asegúrese de seleccionar las opciones **Incluir todos los certificados en la ruta de certificación** y **Exportar todas las propiedades extendidas**. Dicha exportación incluye todos los certificados intermedios, si procede, en el certificado exportado.

<a name="bkmk_subjectaltname"></a>
### Obtención de un certificado SubjectAltName con OpenSSL

OpenSSL se puede usar para crear una solicitud de certificado que use la extensión SubjectAltName para ser compatible con varios nombres de servidor mediante un único certificado; sin embargo, es necesario contar con un archivo de configuración. Los siguientes pasos describen la creación de un archivo de configuración y su modo de empleo para solicitar un certificado.

1. Cree un nuevo archivo con nombre __sancert.cnf__ y use lo siguiente como contenido del archivo:

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default =
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default =
		localityName = Locality Name (eg, city)
		localityName_default =
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  =
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	Observe la línea que empieza por "subjectAltName". Sustituya los nombres de dominio actuales por nombres de dominio que desee que sean admitidos, además del nombre común. Por ejemplo:

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	No es necesario que cambie el campo commonName\_default, dado que se le solicitará que escriba su nombre común en uno de los siguientes pasos.

2. Guarde el archivo __sancert.cnf__.

1. Genere una clave privada y una solicitud de firma de certificado mediante el archivo de configuración sancert.cnf. Desde una sesión Bash o una sesión de Terminal, use el siguiente comando:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Cuando se le solicite, escriba la información apropiada. Por ejemplo:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com


	Al finalizar el proceso, debería tener dos archivos: **myserver.key** y **server.csr**. El archivo **server.csr** contiene la solicitud de firma de certificado.

3. Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] (Programa de certificados raíz de SSL para Windows y Windows Phone 8 (Entidades de certificación miembros)) en Microsoft TechNet Wiki.

4. Una vez que haya obtenido un certificado de una entidad de certificación, guárdelo en un archivo llamado **myserver.crt**. Si la entidad de certificación ha proporcionado el certificado en formato de texto, solo tiene que pegar el texto del certificado en el archivo **myserver.crt**. El contenido del archivo debería parecerse a lo siguiente, al visualizarlo en un editor de texto:

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	Guarde el archivo .

5. En la línea de comandos, una sesión de Bash o una sesión de Terminal, use el siguiente comando para convertir **myserver.key** y **myserver.crt** en **myserver.pfx**, que es el formato que necesita el Servicio de aplicaciones de Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	> [AZURE.NOTE] Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión que se le ha proporcionado como archivo PEM (extensión de archivo .pem).
	>
	> El siguiente comando muestra cómo se crea un archivo .pfx que incluya certificados intermedios, que se encuentra en contiene el archivo **intermediate-cets.pem**:
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Después de ejecutar este comando, debería tener el archivo **myserver.pfx**, que es apropiado para usarlo con el Servicio de aplicaciones de Azure.

<a name="bkmk_selfsigned"></a>
### Expedición de certificados autofirmados (solo para pruebas)

En algunos casos, es posible que desee obtener un certificado de evaluación y posponer la compra a través de una entidad de certificación de confianza hasta que pase a la fase de producción. Los certificados autofirmados pueden resultarle útiles en este caso. Un certificado autofirmado es un certificado que puede crear y firmar como si fuera una entidad de certificación. Aunque este certificado se puede usar para proteger una aplicación, la mayoría de los exploradores devolverán errores cuando se visite la aplicación, ya que el certificado no tiene la firma de una entidad de certificación de confianza. Algunos exploradores pueden incluso denegarle el permiso de visualización de la aplicación.

- [Generación de un certificado autofirmado con makecert](#bkmk_ssmakecert)
- [Generación de un certificado autofirmado con OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_ssmakecert"></a>
#### Generación de un certificado autofirmado con makecert ####

Puede crear un certificado de evaluación desde un sistema Windows que tenga instalado Visual Studio mediante los siguientes pasos:

1. En el **menú Inicio** o en la pantalla **Inicio**, busque **Símbolo del sistema para desarrolladores**. Por último, haga clic con el botón derecho en **Símbolo del sistema para desarrolladores** y seleccione **Ejecutar como administrador**.

	Si se muestra el cuadro de diálogo Control de cuentas de usuario, seleccione **Sí** para continuar.

2. Desde el Símbolo del sistema para desarrolladores, use el siguiente comando para crear un nuevo certificado autofirmado. Debe sustituir **serverdnsname** por el DNS de la aplicación.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Este comando creará un certificado que será válido entre las fechas 01/01/2013 y 01/01/2014 y almacenará su ubicación en el almacén de certificados CurrentUser.

3. En el **menú Inicio** o en la pantalla **Inicio**, busque **Windows PowerShell** e inicie esta aplicación.

4. Desde el símbolo del sistema de Windows PowerShell, use los siguientes comandos para exportar el certificado que ha creado previamente:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	Así se almacena la contraseña especificada en forma de cadena segura en $mypwd y, a continuación, se busca el certificado con el nombre DNS especificado por el parámetro **dnsname** y se exporta al archivo especificado por el parámetro **filepath**. La cadena segura que contiene la clave se usa para proteger el archivo exportado.

<a name="bkmk_ssopenssl"></a>
####Generación de un certificado autofirmado con OpenSSL ####

1. Cree un documento nuevo llamado **serverauth.cnf** que tenga el siguiente contenido de este archivo:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	Así se especifican las opciones de configuración necesarias para producir un certificado SSL que pueda usar el Servicio de aplicaciones de Azure.

2. Genere un nuevo certificado autofirmado con lo siguiente desde una línea de comandos, una sesión Bash o una sesión de Terminal:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Así se crea un certificado nuevo con las opciones de configuración especificadas en el archivo **serverauth.cnf**.

3. Para exportar el certificado a un archivo .PFX que se pueda subir a una aplicación del Servicio de aplicaciones de Azure, use el comando siguiente:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	El archivo **myserver.pfx** generado por este comando se puede usar para proteger la aplicación en las pruebas.

<a name="bkmk_standardmode"></a>
## 2\. Configurar el plan de tarifa Estándar o Premium

La habilitación de HTTPS para un dominio personalizado solo está disponible para los niveles **Estándar** y **Premium** del Servicio de aplicaciones de Azure. Siga estos pasos para cambiar el plan del Servicio de aplicaciones al nivel **Estándar**.

> [AZURE.NOTE] Antes de cambiar una aplicación del nivel **Gratis** al nivel **Estándar**, debe eliminar los límites de gasto en vigor para la suscripción; de lo contrario, se arriesga a que la aplicación deje de estar disponible si alcanza dichos límites antes de que finalice el período de facturación. Para obtener más información sobre los precios de los niveles compartido y **Estándar**, consulte [Información detallada de precios][pricing].

1.	En el explorador, abra el [Portal de Azure](https://portal.azure.com).
	
2.	Haga clic en la opción **Servicio de aplicaciones** en el lado izquierdo de la página.

4.	Haga clic en el nombre de la aplicación.

5.	En la página **Essentials**, haga clic en **Configuración**.

6.	Haga clic en **Escalar verticalmente**.
	
	![Pestaña SCALE][scale]

7.	En la sección **Escalar verticalmente**, establezca el modo del plan del Servicio de aplicaciones haciendo clic en **Seleccionar**.

	> [AZURE.NOTE] Si se muestra el error "Error al configurar la escala de la aplicación web '&lt;nombre de aplicación'&gt;", puede usar el botón Detalles para obtener más información. Puede que reciba un error "Not enough available standard instance servers to satisfy this request". Si se muestra este error, póngase en contacto con el [soporte técnico de Azure](/support/options/).

<a name="bkmk_configuressl"></a>
## 3\. Configurar SSL en la aplicación

Antes de realizar los pasos de esta sección, debe haber asociado un nombre de dominio personalizado a la aplicación. Para obtener más información, consulte [Configuración de un nombre de dominio personalizado para una aplicación web][customdomain].

1.	En el explorador, abra el [Portal de Azure](https://portal.azure.com).

2.	Haga clic en la opción **Servicio de aplicaciones** en el lado izquierdo de la página.

4.	Haga clic en el nombre de la aplicación.

5.	En la página **Essentials**, haga clic en **Configuración**.

6.	Haga clic en **Dominios personalizados y SSL**.

	![Pestaña Configuración][configure]

7.	En la sección **Certificados**, haga clic en **Cargar**.

8.	Mediante el cuadro de diálogo **Cargar un certificado**, seleccione el archivo de certificado .pfx que ha creado previamente con Administrador de IIS u OpenSSL. Especifique la contraseña, si procede, que se utilizó para proteger el archivo .pfx. Por último, haga clic en **Guardar** para cargar el certificado.

	![carga de ssl][uploadcert]

9. En la sección **Enlaces SSL** de la pestaña **Configuración de SSL**, use las listas desplegables para seleccionar el nombre de dominio que desea proteger con SSL y el certificado que va a usar. Es posible que también desee seleccionar el uso de SSL basada en IP o en la extensión [Indicación de nombre de servidor][sni] (SNI).

	![enlaces ssl][sslbindings]

	* El modo SSL basado en IP asocia un certificado a un nombre de dominio mediante la asignación de una dirección IP pública dedicada del servidor al nombre de dominio. En este caso es necesario que cada nombre de dominio (contoso.com, fabrikam.com, etc.) asociado a un servicio tenga una dirección IP dedicada. Este es el método tradicional de asociación de certificados SSL a un servidor web.

	* La encriptación SSL basada en SNI es una extensión de SSL y [Seguridad de la capa de transporte][tls] (TLS) que permite que varios dominios compartan la misma dirección IP con certificados de seguridad independientes para cada dominio. Los exploradores más modernos (entre los que se incluyen Internet Explorer, Chrome, Firefox y Opera) son compatibles con la extensión SNI; sin embargo, es posible que los exploradores más antiguos no sean compatibles con la extensión SNI. Para obtener más información acerca de la extensión SNI, consulte el artículo [Indicación de nombre de servidor][sni] en Wikipedia.

10. Haga clic en **Guardar** para guardar los cambios y habilitar SSL.

> [AZURE.NOTE] Si ha seleccionado **SSL basada en IP** y su dominio personalizado se ha configurado con un registro D, debe realizar los siguientes pasos adicionales:
>
> 1. Después de haber configurado un enlace SSL basado en IP, se asigna una dirección IP dedicada a la aplicación. Esta dirección IP se puede encontrar en la página **Panel** de la aplicación, en la sección **vista rápida**. Se mostrará como **Dirección IP virtual**:
>    
>     ![Dirección IP virtual](./media/configure-ssl-web-site/staticip.png)
>    
>     Tenga en cuenta que esta dirección IP será distinta de la dirección IP virtual que ha usado previamente para configurar el registro D de su dominio. Si lo ha configurado para usar un cifrado SSL basado en SNI, o no lo ha configurado para usar SSL, esta entrada no contendrá ninguna dirección.
>
> 2. Mediante las herramientas proporcionadas por su registrador de nombres de dominio, modifique el registro D de su nombre de dominio personalizado para que apunte a la dirección IP del paso anterior.


Llegados a este punto, debería poder visitar la aplicación web con `HTTPS://`, en lugar de `HTTP://`, para comprobar que el certificado se ha configurado correctamente.

<a name="bkmk_enforce"></a>
## 4\. Implementar HTTPS en la aplicación

Servicio de aplicaciones de Azure *no* exige el uso de HTTPS. Los visitantes pueden seguir accediendo a la aplicación a través de HTTP, lo que puede suponer un riesgo para la seguridad de esta. Si desea implementar HTTPS en la aplicación, puede utilizar el módulo **URL Rewrite**. El módulo URL Rewrite se incluye en el Servicio de aplicaciones de Azure y permite definir reglas que se aplican a las solicitudes entrantes antes de que las solicitudes lleguen a la aplicación. **Se puede utilizar para aplicaciones escritas en cualquier lenguaje de programación compatible con Azure.**

> [AZURE.NOTE] Las aplicaciones .NET MVC deben utilizar el filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx), en lugar de URL Rewrite. Para obtener más información acerca del uso de RequireHttps, consulte [Implementar una aplicación ASP.NET MVC 5 segura en una aplicación web](../articles/app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
>
> Para obtener información sobre la redirección programática de las solicitudes utilizando otros lenguajes y marcos de programación, consulte la documentación de esas tecnologías.

Las reglas de URL Rewrite se definen en el archivo **web.config**, que se almacena en la raíz de la aplicación. El ejemplo siguiente contiene una regla básica de URL Rewrite que impone el uso de HTTPS a todo el tráfico entrante.

<a name="example"></a>**Web.Config de URL Rewrite de ejemplo**

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

Esta regla funciona devolviendo un código de estado HTTP de 301 (redirección permanente) cuando el usuario solicita una página mediante HTTP. El 301 redirige la solicitud a la misma URL que el visitante solicitó, pero sustituye la parte de HTTP de la solicitud por HTTPS. Por ejemplo, HTTP://contoso.com se redirige a HTTPS://contoso.com.

> [AZURE.NOTE] Si su aplicación está escrita en **Node.js**, **PHP**, **Python Django** o **Java**, es probable que no incluya un archivo web.config. Sin embargo, **Node.js**, **Python Django** y **Java** usan un archivo web.config cuando está hospedado en el Servicio de aplicaciones de Azure (Azure crea el archivo automáticamente durante la implementación, por lo que el usuario nunca lo ve). Si incluye uno como parte de su aplicación, sobrescribirá el que Azure genera automáticamente.

###.NET

Para aplicaciones. NET, modifique el archivo web.config de la aplicación y agregue la sección **&lt;rewrite>** del [ejemplo](#example) a la sección **&lt;system.WebServer>**.

Si el archivo web.config ya incluye una sección **&lt;rewrite>**, agregue la **&lt;regla>** del [ejemplo](#example) como la primera entrada de la sección **&lt;reglas>**.

###PHP

En el caso de las aplicaciones PHP, guarde el [ejemplo](#example) como archivo web.config en la raíz de la aplicación y, a continuación, implemente de nuevo la aplicación en su aplicación.

###Node.js, Python Django y Java

Se crea automáticamente un archivo web.config para aplicaciones Node.js, Python Django y Java si estas no proporcionan uno, pero dicho archivo solo existe en el servidor porque se crea durante la implementación. El archivo generado automáticamente contiene valores de configuración que le indican a Azure cómo hospedar su aplicación.

Para recuperar y modificar el archivo generado automáticamente desde la aplicación, siga los pasos que se indican a continuación.

1. Descargue el archivo mediante FTP (consulte [Carga y descarga de archivos a través de FTP y recopilación de registros de diagnósticos](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Agréguelo a la raíz de su aplicación.

3. Agregue las reglas de reescritura utilizando la siguiente información.

	* **Node.js y Python Django**

		El archivo web.config generado para las aplicaciones Node.js y Python Django ya tendrá una sección **&lt;rewrite>**, que contendrá las entradas de **&lt;rule>** necesarias para el funcionamiento correcto de la aplicación. Para forzar el uso de HTTPS en la aplicación, agregue la línea **&lt;rule>** del ejemplo como primera entrada de la sección **&lt;rules>**. Esto forzará el uso de HTTPS y dejará el resto de reglas intactas.

	* **Java**

		El archivo web.config para aplicaciones Java que utilizan Apache Tomcat no contiene una sección **&lt;rewrite>**, por lo que debe agregar la sección **&lt;rewrite>** del ejemplo a la sección **&lt;system.webServer>**.

4. Implemente el proyecto de nuevo (incluyendo en archivo web.config actualizado) en Azure.

Una vez que implemente un archivo web.config con una regla de reescritura para imponer el uso de HTTPS, esta debería surtir efecto de inmediato y redirigir todas las solicitudes a HTTPS.

Para obtener más información sobre el módulo URL Rewrite de IIS, consulte la documentación de [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite).

## Más recursos ##
- [Centro de confianza de Microsoft Azure](/support/trust-center/security/)
- [Opciones de configuración desbloqueadas en Sitios web Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Activación del registro de diagnósticos](../articles/app-service-web/web-sites-enable-diagnostic-log.md)
- [Configuración de Aplicaciones web en Servicio de aplicaciones de Azure](../articles/app-service-web/web-sites-configure.md)
- [Portal de administración de Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

[customdomain]: ../articles/app-service-web/web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png

<!---HONumber=AcomDC_0211_2016-->