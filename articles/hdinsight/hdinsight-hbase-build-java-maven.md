<properties
pageTitle="Crear una aplicación de HBase con Maven y luego implementar en HDInsight basado en Windows | Microsoft Azure"
description="Aprenda a usar Apache Maven para compilar una aplicación de Apache HBase basada en Java e implementarla después en un clúster de HDInsight de Azure basado en Windows."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"
tags="azure-portal"/>

<tags
ms.service="hdinsight"
ms.workload="big-data"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="02/01/2016"
ms.author="larryfr"/>

#Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)

Aprenda a crear y compilar una aplicación de [Apache HBase](http://hbase.apache.org/) en Java con Apache Maven. Luego use la aplicación con Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) es una herramienta de administración y comprensión de proyectos de software que le permite compilar software, documentación e informes para proyectos Java. En este artículo, aprenderá a usarla para crear una aplicación Java básica que crea, consulta y elimina una tabla de HBase en un clúster de HDInsight de Azure.

##Requisitos

* [JDK de la plataforma Java 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o posterior

* [Maven](http://maven.apache.org/)

* [Un clúster de HDInsight de Azure con HBase](hdinsight-hbase-get-started.md#create-hbase-cluster)

##Creación del proyecto

1. Desde la línea de comandos de su entorno de desarrollo, cambie los directorios por la ubicación en la que desea crear el proyecto, por ejemplo, `cd code\hdinsight`.

2. Use el comando __mvn__, que se instala con Maven, para generar el scaffolding del proyecto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Esta acción creará un directorio en el directorio actual, con el nombre especificado por el parámetro __artifactID__ (**hbaseapp** en este ejemplo). Este directorio contendrá los siguientes elementos:

    * __pom.xml__: el modelo de objetos de proyectos ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contiene la información y los detalles de configuración usados para compilar el proyecto.

    * __src__: directorio que contiene a su vez el directorio __main\\java\\com\\microsoft\\examples__, donde creará la aplicación.

3. Elimine el archivo __src\\test\\java\\com\\microsoft\\examples\\apptest.java__, puesto que no se usará en este ejemplo.

##Actualización del modelo de objetos de proyectos

1. Edite el archivo __pom.xml__ y agregue lo siguiente dentro de la sección `<dependencies>`:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    Esta acción le indica a Maven que el proyecto requiere __hbase-client__, versión __0.98.4-hadoop2__. En el momento de compilación, esto se descargará desde el repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio central de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para ver más información sobre esta dependencia.

2. Agregue el siguiente código al archivo __pom.xml__. Esto debe estar dentro de las etiquetas `<project>...</project>` en el archivo; por ejemplo, entre `</dependencies>` y `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
              </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>
          </plugins>
        </build>

    Esta acción configura un recurso (__conf\\hbase-site.xml__,) que contiene información de configuración para HBase.

    > [AZURE.NOTE] También puede establecer los valores de configuración mediante código. Vea los comentarios del ejemplo __CreateTable__ que sigue para ver cómo hacer esto.

    Esta acción también configura [Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) y [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/). El complemento compiler se usa para compilar la topología. El complemento shade se usa para evitar la duplicación de licencias en el paquete JAR compilado por Maven. La razón de usar este complemento es que los archivos de licencia duplicados pueden provocar un error en tiempo de ejecución en el clúster de HDInsight. El uso del complemento maven-shade-plugin con la implementación de `ApacheLicenseResourceTransformer` evita este error.

    El complemento maven-shade-plugin también producirá un uberjar (o fatjar), que contiene todas las dependencias que necesita la aplicación.

3. Guarde el archivo __pom.xml__.

4. Cree un nuevo directorio llamado __conf__ en el directorio __hbaseapp__. En el directorio __conf__, cree un nuevo archivo llamado __hbase-site.xml__ y use el siguiente contenido.

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
          <!-- Uncomment the following if you are using
               a Linux-based HDInsight cluster -->
          <!--
          <property>
            <name>zookeeper.znode.parent</name>
            <value>/hbase-unsecure</value>
          </property>
          -->
        </configuration>

    Este archivo se usará para cargar la configuración de HBase de un clúster de HDInsight.

    > [AZURE.NOTE] Este es un archivo hbase-site.xml mínimo, que contiene la configuración básica elemental del clúster de HDInsight. Para los clústeres basados en Linux, debe quitar los comentarios para la entrada de `zookeeper.znode.parent` para establecer correctamente el znode raíz de Zookeeper que utiliza HBase.

3. Guarde el archivo __hbase-site.xml__.

##Creación de la aplicación

1. Vaya al directorio __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ y cambie el nombre del archivo app.java a __CreateTable.java__.

2. Abra el archivo __CreateTable.java__ y reemplace el contenido existente por lo siguiente:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Esta es la clase __CreateTable__, que creará una tabla llamada __people__ y la rellenará con algunos usuarios predefinidos.

3. Guarde el archivo __CreateTable.java__.

4. En el directorio __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__, cree un nuevo archivo denominado __SearchByEmail.java__. Use el siguiente contenido para este archivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    La clase __SearchByEmail__ se puede usar para consultar filas por dirección de correo electrónico. Dado que esta clase usa un filtro de expresiones regulares, puede proporcionar una cadena o una expresión regular cuando la utilice.

5. Guarde el archivo __SearchByEmail.java__.

6. En el directorio __hbaseapp\\src\\main\\hava\\com\\microsoft\\examples__, cree un nuevo archivo denominado __DeleteTable.java__. Use el siguiente contenido para este archivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Esta clase es solo para limpiar este ejemplo. Para ello, primero se deshabilita y luego se elimina la tabla creada por la clase __CreateTable__.

7. Guarde el archivo __DeleteTable.java__.

##Compilación y empaquetado de la aplicación

1. Abra un símbolo del sistema y cambie los directorios por el directorio __hbaseapp__.

2. Use el siguiente comando para compilar un archivo JAR que contenga la aplicación:

        mvn clean package

    Esta acción eliminará los artefactos de compilación anteriores, descargará las dependencias que no se hayan instalado aún y luego compilará y empaquetará la aplicación.

3. Cuando el comando termine de ejecutarse, el directorio __hbaseapp\\target__ contendrá un archivo llamado __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] El archivo __hbaseapp-1.0-SNAPSHOT.jar__ es un uberjar (en ocasiones llamado fatjar) que contiene todas las dependencias necesarias para ejecutar la aplicación.

##Carga del archivo JAR e inicio de un trabajo

> [AZURE.NOTE] Existen muchas formas de cargar un archivo en el clúster de HDInsight, tal y como se describe en [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md). Los pasos siguientes usan [Azure PowerShell](../powershell-install-configure.md).

1. Tras instalar y configurar Azure PowerShell, cree un nuevo archivo llamado __hbase-runner.psm1__. Use el siguiente contenido para este archivo:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup `
                | %{ $_.Key1 }
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Este archivo contiene dos módulos:

    * __Add-HDInsightFile__: se usa para cargar archivos en HDInsight.

    * __Start-HBaseExample__: se usa para ejecutar las clases creadas anteriormente.

2. Guarde el archivo __hbase-runner.psm1__.

3. Abra una nueva ventana de Azure PowerShell, cambie los directorios por el directorio __hbaseapp__ y luego ejecute el siguiente comando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Cambie la ruta de acceso a la ubicación del archivo __hbase-runner.psm1__ creado anteriormente. Esta acción registrará el módulo para esta sesión de Azure PowerShell.

2. Use el comando siguiente para cargar el archivo __hbaseapp-1.0-SNAPSHOT.jar__ en el clúster de HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Reemplace __hdinsightclustername__ por el nombre del clúster de HDInsight. El comando cargará a continuación el archivo __hbaseapp-1.0-SNAPSHOT.jar__ en la ubicación __example/jars__ del almacenamiento principal del clúster de HDInsight.

3. Una vez cargados los archivos, use el comando siguiente para crear una nueva tabla con __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Reemplace __hdinsightclustername__ por el nombre del clúster de HDInsight.

    Este comando creará una nueva tabla llamada __people__ en el clúster de HDInsight. Este comando no muestra ninguna salida en la ventana de la consola.

2. Para buscar entradas en la tabla, use el siguiente comando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Reemplace __hdinsightclustername__ por el nombre del clúster de HDInsight.

    Este comando usa la clase **SearchByEmail** para buscar filas donde la familia de columnas __contactinformation__ y la columna __email__ contengan la cadena __contoso.com__. Debe recibir los siguientes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Si se usa __fabrikam.com__ como valor de `-emailRegex` se devolverán los usuarios que tienen __fabrikam.com__ en el campo de correo electrónico. Dado que esta búsqueda se implementa usando un filtro basado en una expresión regular, también puede escribir expresiones regulares como __^r__, lo cual devolverá entradas en las que el correo electrónico empiece por la letra 'r'.

##Eliminación de la tabla

Cuando haya terminado con el ejemplo, use el siguiente comando desde la sesión de Azure PowerShell para eliminar la tabla __people__ usada en este ejemplo:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Reemplace __hdinsightclustername__ por el nombre del clúster de HDInsight.

##Solución de problemas

###Sin resultados o resultados inesperados al usar Start-HBaseExample

Use el parámetro `-showErr` para ver el error estándar (STDERR) producido mientras se ejecutaba el trabajo.

<!---HONumber=AcomDC_0218_2016-->