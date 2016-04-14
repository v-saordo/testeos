<properties 
	pageTitle="Creación de artefactos personalizados para la máquina virtual del Laboratorio de desarrollo y pruebas | Microsoft Azure"
	description="Aprenda a crear sus propios artefactos para usarlos con laboratorios de desarrollo y pruebas"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="tarcher"/>

#Creación de artefactos personalizados para la máquina virtual del Laboratorio de desarrollo y pruebas

> [AZURE.NOTE] Haga clic en el vínculo siguiente para ver el vídeo que acompaña a este artículo: [Cómo crear artefactos personalizados](/documentation/videos/how-to-author-custom-artifacts)

## Información general
Los **artefactos** se utilizan para implementar y configurar la aplicación después de aprovisionar una máquina virtual. Un artefacto consta de un archivo de definición de artefacto y otros archivos de script que se almacenan en un repositorio de Git. Los archivos de definición de artefacto constan de JSON y expresiones que puede utilizar para especificar lo que desea instalar en una máquina virtual. Por ejemplo, puede definir el nombre del artefacto, el comando que se va a ejecutar y los parámetros que están disponibles cuando se ejecuta el comando. Puede hacer referencia a otros archivos de script en el archivo de definición de artefacto por nombre.

##Formato del archivo de definición de artefacto
En el ejemplo siguiente se muestran las secciones que componen la estructura básica de un archivo de definición.

	{
	  "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
	  "title": "",
	  "description": "",
	  "iconUri": "",
	  "targetOsType": "",
	  "parameters": {
	    "<parameterName>": {
	      "type": "",
	      "displayName": "",
	      "description": ""
	    }
	  },
	  "runCommand": {
	    "commandToExecute": ""
	  }
	}

| Nombre del elemento | ¿Necesario? | Descripción
| ------------ | --------- | -----------
| $schema | No | Ubicación del archivo de esquema JSON que ayuda a probar la validez del archivo de definición.
| título | Sí | Nombre del artefacto que se muestra en el laboratorio.
| description | Sí | Descripción del artefacto que se muestra en el laboratorio.
| iconUri | No | Identificador URI del icono que se muestra en el laboratorio.
| targetOsType | Sí | Sistema operativo de la máquina virtual donde se instalará el artefacto. Las opciones admitidas son Windows y Linux.
| parameters | No | Los valores que se proporcionan cuando el comando de instalación del artefacto se ejecuta en un equipo. Esto ayuda a personalizar el artefacto.
| runCommand | Sí | Comando de instalación de artefacto que se ejecuta en una máquina virtual.

###Parámetros de artefacto

En la sección de parámetros del archivo de definición, especifique los valores que un usuario puede indicar al instalar un artefacto. Puede hacer referencia a estos valores en el comando de instalación del artefacto.

Defina parámetros con la estructura siguiente:

	"parameters": {
	    "<parameterName>": {
	      "type": "<type-of-parameter-value>",
	      "displayName": "<display-name-of-parameter>",
	      "description": "<description-of-parameter>"
	    }
	  }

| Nombre del elemento | ¿Necesario? | Descripción
| ------------ | --------- | -----------
| type | Sí | Tipo del valor de parámetro. Vea la siguiente lista para conocer los tipos permitidos:
| displayName Yes | Nombre del parámetro que se muestra a un usuario en el laboratorio.
| description | Sí | Descripción del parámetro que se muestra en el laboratorio.

Los tipos permitidos son:

- string: cualquier cadena JSON válida
- int: cualquier entero JSON válido
- bool: cualquier booleano JSON válido
- array: cualquier matriz JSON válida

##Expresiones y funciones de artefacto

Puede utilizar la expresión y las funciones para construir el comando de instalación del artefacto. Las expresiones se incluyen entre corchetes ([ y ]) y se evalúan cuando se instala el artefacto. Pueden aparecer expresiones en cualquier lugar de un valor de cadena JSON y devolver siempre otro valor JSON. Si necesita usar una cadena literal que comienza por un corchete [, debe usar dos corchetes [[. Normalmente, se utilizan expresiones con funciones para construir un valor. Al igual que en JavaScript, las llamadas de función tienen el formato functionName(arg1,arg2,arg3).

La siguiente lista muestra las funciones comunes.

- Parameters(ParameterName): devuelve un valor de parámetro que se proporciona cuando se ejecuta el comando de artefacto.
- concat(arg1,arg2,arg3, …..): combina varios valores de cadena. Esta función puede tomar cualquier número de argumentos.

En el ejemplo siguiente se muestra cómo utilizar expresiones y funciones para construir un valor.

	runCommand": {
	     "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
	, ' -RawPackagesList ', parameters('packages')
	, ' -Username ', parameters('installUsername')
	, ' -Password ', parameters('installPassword'))]"
	}

##Creación de un artefacto personalizado

Cree su artefacto personalizado siguiendo estos pasos:

1. Instale un editor de JSON: necesitará un editor de JSON para trabajar con los archivos de definición del artefacto. Se recomienda usar el [código de Visual Studio](https://code.visualstudio.com/), que está disponible para Windows, Linux y OS X.

1. Obtenga un artifactfile.json de ejemplo: consulte los artefactos creados por el equipo de Laboratorios de desarrollo y pruebas de Azure en nuestro [repositorio de GitHub](https://github.com/Azure/azure-devtestlab), donde hemos creado una completa biblioteca de artefactos que le ayudará a crear sus propios artefactos. Descargue un archivo de definición de artefacto y haga cambios sobre él para crear sus propios artefactos.

1. Haga uso de IntelliSense: aproveche IntelliSense para ver elementos válidos que se pueden utilizar para construir un archivo de definición de artefacto. También puede ver las distintas opciones para los valores de un elemento. Por ejemplo, IntelliSense le muestra las dos opciones de Windows o Linux al editar el elemento **targetOsType**.

1. Almacenamiento del artefacto en un repositorio de Git
	1. Cree un directorio independiente para cada artefacto donde el nombre del directorio sea el mismo que el nombre del artefacto.
	1. Almacene el archivo de definición de artefacto (artifactfile.json) en el directorio que ha creado.
	1. Almacene los scripts a los que hace referencia el comando de instalación del artefacto.

	Este es un ejemplo del aspecto que tendrá una carpeta de artefacto:

	![Ejemplo de repositorio de Git de artefacto](./media/devtest-lab-artifact-author/git-repo.png)

1. Agregue el repositorio de artefactos al laboratorio: consulte el artículo [Adición de un repositorio de artefactos de Git al Laboratorio de desarrollo y pruebas.](devtest-lab-add-artifact-repo.md).

## Pasos siguientes

- Aprenda cómo [agregar un repositorio de artefactos de Git al Laboratorio de desarrollo y pruebas](devtest-lab-add-artifact-repo.md).

<!---HONumber=AcomDC_0224_2016-->