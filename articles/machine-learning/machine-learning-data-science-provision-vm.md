<properties 
	pageTitle="Aprovisionamiento de Microsoft Data Science Virtual Machine | Microsoft Azure" 
	description="Configuro y creación de Data Science Virtual Machine en Azure para realizar análisis y aprendizaje de equipo." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="bradsev" />


# Aprovisionamiento de Microsoft Data Science Virtual Machine

## Introducción

Microsoft Data Science Virtual Machine es una imagen de máquina virtual (VM) de Azure preinstalada y configurada con varias herramientas populares que se usan habitualmente para el análisis de datos y el aprendizaje automático. Las herramientas incluidas son:

- Microsoft R Server Developer Edition
- Anaconda Python Distribution
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition
- SDK de Azure


La ciencia de datos implica una iteración en una secuencia de tareas: encontrar, cargar y preprocesar datos, crear y probar modelos e implementar los modelos para su uso en aplicaciones inteligentes. No es raro que los científicos de datos usen diversas herramientas para realizar estas tareas. Puede ser bastante lento encontrar las versiones del software adecuadas, descargarlas e instalarlas. Microsoft Data Science Virtual Machine puede facilitar esta carga.

El salto de Microsoft Data Science Virtual Machine inicia el proyecto de análisis. Le permite trabajar en tareas en una variedad de lenguajes, incluidos R, Python, SQL y C#. Visual Studio proporciona un IDE para desarrollar y probar el código que es fácil de usar. El SDK de Azure incluido en la máquina virtual permite crear aplicaciones con varios servicios en la plataforma en la nube de Microsoft.

No hay ningún cargo de software para esta imagen de VM de ciencia de datos. Solo paga por las cuotas de uso de Azure, que dependen del tamaño de la máquina virtual que aprovisionará con esta imagen de VM. Puede encontrar más detalles sobre las cuotas de proceso [aquí](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Requisitos previos

Antes de poder crear una Microsoft Data Science Virtual Machine, debe tener lo siguiente:

- **Una suscripción a Azure**: para conseguir una, vea [Obtención de una evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Una cuenta de almacenamiento de Azure**: para crear una, consulte [Creación de una cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account). También puede crear una cuenta de almacenamiento como parte del proceso de creación de la VM si no desea usar una cuenta existente.


## Creación de su Microsoft Data Science Virtual Machine

Estos son los pasos para crear una instancia de Microsoft Data Science Virtual Machine:

1.	Navegue a la lista de máquinas virtuales en el [Portal de Azure](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.	 Haga clic en el botón **Crear** ubicado en la parte inferior para acceder a un asistente.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 Las secciones siguientes proporcionan las **entradas** para cada uno de los **5 pasos** (enumerados a la derecha de la ilustración anterior) en el asistente que se usó para crear la Microsoft Data Science Virtual Machine. Estas son las entradas necesarias para configurar cada uno de estos pasos:

  **a. Básico**:

   - **Nombre**: nombre del servidor de ciencia de datos que está creando.
   - **Nombre de usuario**: identificador de inicio de sesión de la cuenta del administrador
   - **Contraseña**: contraseña de la cuenta del administrador
   - **Suscripción**: si tiene más de una suscripción, seleccione en la que se creará y facturará la máquina
   - **Grupo de recursos**: puede crear uno nuevo o usar un grupo ya existente
   - **Ubicación**: seleccione el centro de datos más adecuado. Normalmente es el centro de datos que tenga la mayoría de los datos o que esté más cercano a su ubicación física para un acceso más rápido a la red

  **b. Tamaño**:

   - Seleccione uno de los tipos de servidor que cumpla sus requisitos funcionales y las restricciones de costo. Para obtener más opciones de tamaños de la máquina virtual, seleccione "Ver todo"

  **c. Configuración**

   - **Tipo de disco**: elija Premium si prefiere una unidad de estado sólido (SSD), de lo contrario elija "Estándar".
   - **Cuenta de almacenamiento**: puede crear una nueva cuenta de almacenamiento de Azure en su suscripción o usar uno existente en la misma *ubicación* que ha elegido en el paso Básico del asistente.
   - **Otros parámetros**: en la mayoría de los casos, usará simplemente los valores predeterminados. Puede mover el puntero sobre el vínculo informativo para obtener ayuda sobre los campos específicos en caso de que desee considerar el uso de valores no predeterminados.

  **d. Resumen**:

   - Compruebe que toda la información que ha especificado es correcta.

  **e. Comprar**:

   - Haga clic en **Comprar** para iniciar el aprovisionamiento. Se proporciona un vínculo a los términos de la transacción. La máquina virtual no tiene ningún cargo adicional más allá del proceso para el tamaño del servidor que eligió en el paso **Tamaño**. 


El aprovisionamiento tardará entre 10 y 20 minutos. El estado del aprovisionamiento se muestra en el Portal de Azure.

## Acceso a Microsoft Data Science Virtual Machine

Una vez creada la máquina virtual, puede iniciar sesión en ella mediante el escritorio remoto con las credenciales de la cuenta del administrador que creó en la sección Básico del paso 4.

Una vez creada y aprovisionada la máquina virtual, está listo para comenzar a usar las herramientas que se instalan y configuran en ella. Hay iconos del menú de inicio e iconos del escritorio para muchas de las herramientas.

## Creación de una contraseña segura en el servidor Jupyter Notebook 

Ejecute el siguiente comando desde la línea de comandos en Data Science Virtual Machine a fin de crear su propia contraseña segura para el servidor Jupyter Notebook instalado en la máquina.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Elija una contraseña segura cuando se le solicite.

Verá el hash de contraseña en el formato "sha1:xxxxxx" en la salida. Copie este hash de contraseña y reemplace el hash existente que se encuentra en el archivo de configuración del cuaderno que se encuentra en: **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py** por un nombre de parámetro ***c.NotebookApp.password***.

Solo debe reemplazar el valor de hash existente que se encuentra entre comillas. Deben conservarse las comillas y el prefijo ***sha1:*** del valor del parámetro.

Por último, debe detener y reiniciar el servidor de Ipython que se ejecuta en la máquina virtual como una tarea programada de Windows denominada "Start\_IPython\_Notebook". Si no se acepta la nueva contraseña después de reiniciar esta tarea, pruebe a reiniciar la máquina virtual.

## Herramientas instaladas en Microsoft Data Science Virtual Machine

### Microsoft R Server Developer Edition
Si desea usar R para su análisis, la máquina virtual tiene Microsoft R Server Developer Edition instalado. Microsoft R Server es una plataforma de análisis de nivel empresarial que se puede implementar ampliamente gracias a que R es compatible, escalable y seguro. Compatible con una gran variedad de estadísticas de macrodatos, con funcionalidades de modelado de predicción y de aprendizaje automático, R Server admite toda la gama de análisis: exploración, análisis, visualización y modelado. Al usar y ampliar R de código abierto, Microsoft R Server es totalmente compatible con scripts, funciones y paquetes CRAN de R, a fin de analizar datos a escala empresarial. También resuelve las limitaciones de memoria de R de código abierto al agregar el procesamiento en paralelo y fragmentado de datos a Microsoft R Server, lo que permite a los usuarios ejecutar análisis en volúmenes de datos más grandes de lo que cabe en la memoria principal. También se encuentra un IDE para R empaquetado en la máquina virtual, al que se puede acceder haciendo clic en el icono "Revolution R Enterprise 8.0" en el menú Inicio o en el escritorio. Puede descargar y usar otros IDE, así como [RStudio](http://www.rstudio.com).

### Python
Para el desarrollo con Python, se ha instalado Anaconda Python Distribution 2.7 y 3.5. Esta distribución contiene Python base, junto con aproximadamente 300 de los paquetes de matemáticas, ingeniería y análisis de datos más populares. Puede usar Herramientas de Python para Visual Studio (PTVS) que se instala en la edición de Visual Studio 2015 Community o uno de los IDE incluidos con Anaconda, como IDLE o Spyder. Para iniciar uno de ellos, busque en la barra de búsqueda (tecla **Win** + **S**). **Nota**: A fin de señalar las Herramientas de Python para Visual Studio en Anaconda Python 2.7 y 3.5, debe crear entornos personalizados entornos para cada versión; para ello, vaya a Herramientas -> Herramientas de Python -> Entornos de Python y, a continuación, haga clic en "+ Personalizar" en Visual Studio 2015 Community Edition y establecer las rutas de acceso de entorno. Anaconda Python 2.7 se instala en C:\\Anaconda y Anaconda Python 3.5 se instala en c:\\Anaconda\\envs\\py35. Consulte la [documentación de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obtener pasos detallados.

### Jupyter Notebook
La distribución de Anaconda también incluye un cuaderno de Jupyter Notebook, un entorno para compartir código y análisis. Se ha configurado previamente un servidor Jupyter Notebook con kernels de Python 2, Python 3 y R. Hay un icono del escritorio llamado "Jupyter Notebook" para iniciar el explorador y tener acceso al servidor Notebook. Si está en la máquina virtual a través de un escritorio remoto, también puede visitar [https://localhost:9999/](https://localhost:9999/) para tener acceso al servidor Jupyter Notebook (nota: si recibe alguna advertencia de certificado, continúe). Hemos empaquetado cuadernos de ejemplo (uno en Python y otro en R). Puede ver el vínculo a los ejemplos en la página de inicio del cuaderno después de que se autentique en Jupyter Notebook con la contraseña creada en el paso anterior.

### Visual Studio 2015 Community Edition
Visual Studio Community Edition instalado en la máquina virtual. Es una versión gratuita del popular IDE de Microsoft que puede usar para fines de evaluación y para equipos muy pequeños. Puede revisar los términos de licencia [aquí](https://www.visualstudio.com/support/legal/mt171547). Haga doble clic en el icono del escritorio o en el menú **Inicio** para abrir Visual Studio. También puede buscar programas con **Win** + **S** y escribiendo "Visual Studio". Una vez ahí, puede crear proyectos en lenguajes como C# y Python. También encontrará complementos instalados que resultan prácticos para trabajar con servicios de Azure como Catálogo de datos de Azure, HDInsight de Azure (Hadoop, Spark) y Azure Data Lake.

Nota: puede obtener un mensaje que indica que el período de evaluación ha caducado. Puede escribir las credenciales de la cuenta de Microsoft o crear una cuenta y volver a escribirlas para obtener acceso a Visual Studio Community Edition.

### SQL Server Express
También se ha empaquetado una versión limitada de SQL Server con Visual Studio Community Edition. Para tener acceso a SQL Server, inicie **SQL Server Management Studio**. El nombre de la máquina virtual se rellenará como Nombre del servidor. Use Autenticación de Windows cuando inicie sesión como administrador en Windows. Una vez que esté en SQL Server Management Studio, puede crear otros usuarios, crear bases de datos, importar datos y ejecutar consultas SQL.

### Las tablas de Azure 
Varias herramientas de Azure están instaladas en la máquina virtual: - Hay un acceso directo de escritorio para obtener acceso a la documentación del SDK de Azure. - **AzCopy** se usa para pasar datos a y desde la cuenta de Almacenamiento de Microsoft Azure. - **Explorador de almacenamiento de Azure** se usa para explorar los objetos que haya almacenado en la cuenta de Almacenamiento de Azure. - **Microsoft Azure Powershell** es una herramienta utilizada para administrar los recursos de Azure en el lenguaje de scripts de Powershell que también se instala en su máquina virtual.

###Power BI

Para ayudarle a crear paneles y visualizaciones excelentes, se instaló **Power BI Desktop**. Use esta herramienta para extraer datos de orígenes diferentes, para crear los paneles e informes y publicarlos en la nube. Para obtener información, consulte el sitio de [Power BI](http://powerbi.microsoft.com).

Nota: se necesita una cuenta de Office 365 para tener acceso a Power BI.

## Herramientas de desarrollo de Microsoft adicionales
Puede usar el [**Instalador de plataforma web de Microsoft**](https://www.microsoft.com/web/downloads/platform.aspx) para detectar y descargar otras herramientas de desarrollo de Microsoft. También hay un acceso directo a la herramienta que se proporciona en el escritorio de Microsoft Data Science Virtual Machine.

## Pasos siguientes
Estos son algunos pasos para proseguir con el aprendizaje y la exploración.

* Explore las diversas herramientas de Data Science Virtual Machine haciendo clic en el menú Inicio y comprobando las herramientas incluidas en el menú.
* Vaya a **C:\\Archivos de programa\\Microsoft\\MRO-for-RRE\\8.0\\R-3.2.2\\library\\RevoScaleR\\demoScripts** para ver ejemplos de uso de la biblioteca RevoScaleR de R que admite análisis de datos a escala empresarial.  
* Aprenda a crear soluciones analíticas de un extremo a otro mediante el uso sistemático del [proceso de ciencia de datos](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)
* Visite [Cortana Analytics Gallery (Galería de análisis de Cortana)](http://gallery.cortanaanalytics.com) para ver ejemplos de aprendizaje automático y de análisis de datos con Cortana Analytics Suite. También hemos proporcionado un icono en el menú Inicio y en el escritorio de la máquina virtual para facilitar el acceso 

<!---HONumber=AcomDC_0211_2016-->