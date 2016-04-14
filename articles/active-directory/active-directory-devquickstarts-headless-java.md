<properties
	pageTitle="Introducción a Java de Azure AD | Microsoft Azure"
	description="Cómo crear una aplicación de línea de comandos de Java que inicie la sesión de usuarios para tener acceso a una API."
	services="active-directory"
	documentationCenter="java"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="brandwe"/>


# Utilización de una aplicación de línea de comandos de Java para tener acceso a una API con Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD facilita la externalización de la administración de identidad de su aplicación web, proporcionando un inicio y cierre de sesión únicos con solo unas pocas líneas de código. En las aplicaciones web Java, puede realizar esto con la implementación de Microsoft del ADAL4J orientado a la comunidad.

  Aquí, usaremos ADAL4J para:
- Iniciar sesión del usuario en la aplicación con Azure AD como proveedor de identidades.
- Mostrar información sobre el usuario.
- Cerrar la sesión del usuario en la aplicación.

Para ello, deberá hacer lo siguiente:

1. Registro de una aplicación con Azure AD
2. Configure la aplicación para usar la biblioteca ADAL4J.
3. Uso de la biblioteca ADAL4J para emitir solicitudes de inicio y cierre de sesión en Azure AD
4. Imprima datos sobre el usuario.

Para empezar, [descargue el esquema de la aplicación](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip). También necesitará a un inquilino de Azure AD en el que registrar la aplicación. Si aún no tiene uno, [descubra cómo conseguirlo](active-directory-howto-tenant.md).

## 1\. Registro de una aplicación con Azure AD
Para habilitar su aplicación a fin de autenticar a los usuarios, primero deberá registrar una nueva aplicación en su inquilino.

- Inicie sesión en el Portal de administración de Azure.
- En el panel de navegación izquierdo, haga clic en **Active Directory**.
- Seleccione el inquilino donde desea registrar la aplicación.
- Haga clic en la pestaña **Aplicaciones** y en Agregar en el cajón inferior.
- Siga las indicaciones y cree una nueva **Aplicación web y/o API web**.
    - El **nombre** de la aplicación describirá su aplicación a los usuarios finales
    - La **dirección URL de inicio de sesión** es la dirección URL base de su aplicación. El valor predeterminado del esquema es `http://localhost:8080/adal4jsample/`.
    - El **URI de id. de aplicación** es un identificador único de su aplicación. La convención consiste en usar `https://<tenant-domain>/<app-name>`, p. ej. `http://localhost:8080/adal4jsample/`
- Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña Configurar.

Una vez en el portal de la aplicación, cree un **Secreto de aplicación ** para la aplicación y cópielo. Lo necesitará en breve.


## 2\. Configuración de la aplicación para que use la biblioteca ADAL4J y requisitos previos con Maven
Aquí configuraremos el ADAL4J para usar el protocolo de autenticación OpenID Connect. ADAL4J se usará para emitir solicitudes de inicio y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

-	En el directorio raíz del proyecto, abra o cree `pom.xml` y busque el `// TODO: provide dependencies for Maven` y reemplácelo por lo siguiente:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>public-client-adal4j-sample</artifactId>
	<packaging>jar</packaging>
	<version>0.0.1-SNAPSHOT</version>
	<name>public-client-adal4j-sample</name>
	<url>http://maven.apache.org</url>
	<properties>
		<spring.version>3.0.5.RELEASE</spring.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>com.microsoft.azure</groupId>
			<artifactId>adal4j</artifactId>
			<version>1.1.2</version>
		</dependency>
		<dependency>
			<groupId>com.nimbusds</groupId>
			<artifactId>oauth2-oidc-sdk</artifactId>
			<version>4.5</version>
		</dependency>
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20090211</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.5</version>
		</dependency>
</dependencies>
	<build>
		<finalName>public-client-adal4j-sample</finalName>
		<plugins>
		        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>install</id>
						<phase>install</phase>
						<goals>
							<goal>sources</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>2.5</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
		</plugins>
	</build>

</project>


```



## 3\. Creación del archivo PublicClient de Java

Como se indicó anteriormente, usaremos API Graph para obtener datos sobre el usuario que inició sesión. Para que nos resulte sencillo, debemos crear un archivo para representar un **objeto de directorio** y un archivo individual para representar al **usuario** para que se pueda usar el modelo orientado a objetos de Java.

1. Cree un archivo llamado `DirectoryObject.java` que usaremos para almacenar los datos básicos sobre cualquier DirectoryObject (puede usarlo más adelante en todas las consultas de Graph que se pueden hacer). Puede cortar y pegar lo siguiente:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##Compilar y ejecutar el ejemplo

Vuelva al directorio raíz y ejecute el siguiente comando para generar el ejemplo que acaba de preparar mediante `maven`. Usará el archivo `pom.xml` que escribió para las dependencias.

`$ mvn package`

Ahora debería tener un archivo `adal4jsample.war` en su directorio `/targets`. Puede implementarlo en el contenedor de Tomcat y visitar la dirección URL

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
Es muy fácil de implementar un WAR con los servidores Tomcat más recientes. Simplemente vaya a `http://localhost:8080/manager/` y siga las instrucciones acerca de cómo cargar el archivo '`adal4jsample.war`. Se implementará automáticamente con el punto de conexión correcto.

##Pasos siguientes

¡Enhorabuena! Ahora tiene una aplicación de Java operativa que tiene la capacidad de autenticar usuarios, realizar llamadas seguras a las API web que usan OAuth 2.0 y obtener información básica sobre el usuario. Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios.

Como referencia, el ejemplo finalizado (sin sus valores de configuración) [se proporciona en forma de archivo .zip aquí](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

<!---HONumber=AcomDC_0302_2016-->