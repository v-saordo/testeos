## Bibliotecas de cliente de Azure para Java: descarga manual

Las bibliotecas de Azure para Java se distribuyen en la [licencia de Apache, versión 2.0][license]. Haga clic [aquí][zip-download] para obtener un archivo ZIP de las bibliotecas y todas sus dependencias. Microsoft Open Technologies, Inc. es quien lo ofrece. Consulte los archivos license.txt y ThirdPartyNotices.txt que se encuentran dentro del ZIP para obtener información sobre la licencia y sobre otras cuestiones.

## Bibliotecas de Azure para Java: Maven

Si su proyecto ya se ha configurado para usar Maven para la compilación, agregue la siguiente dependencia al archivo pom.xml. Nota: para obtener información sobre cómo crear proyectos Maven en Eclipse, que usan las bibliotecas de Azure para Java, consulte [Introducción a las bibliotecas de administración de Azure para Java][maven-getting-started].

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>0.6.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>0.6.0</version>
	</dependency>


En el elemento `<version>`, reemplace los números de versión de este ejemplo por números de versión válidos, que pueden obtenerse del [Repositorio de bibliotecas de Azure en Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

<!---HONumber=Oct15_HO3-->