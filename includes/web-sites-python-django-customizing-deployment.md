Azure determinará que la aplicación use Python **si se cumplen estas condiciones**:

- la carpeta raíz contiene el archivo requirements.txt
- la carpeta raíz contiene cualquier archivo .py o hay un runtime.txt que especifique Python.

Cuando ese es el caso, usará un script de implementación específico de Python, que lleva a cabo la sincronización de archivos, así como otras operaciones de Python como:

- la administración automática del entorno virtual,
- la instalación de paquetes que aparecen en requirements.txt con pip,
- la creación del archivo web.config adecuado en función de la versión de Python seleccionada,
- la recopilación de archivos estáticos para aplicaciones Django.

Puede controlar ciertos aspectos de los pasos de implementación predeterminados sin tener que personalizar el script.

Si desea omitir todos los pasos de implementación específicos de Python, puede crear este archivo vacío:

    \.skipPythonDeployment

Si desea omitir la recopilación de archivos estáticos de la aplicación Django:

    \.skipDjango 

Para obtener más control sobre la implementación, puede invalidar el script de implementación predeterminado mediante la creación de los archivos siguientes:

    \.deployment
    \deploy.cmd

Puede utilizar la [interfaz de línea de comandos de Azure][] para crear los archivos. Use este comando desde la carpeta del proyecto:

    azure site deploymentscript --python

Cuando estos archivos no existen, Azure crea un script de implementación temporal y lo ejecuta. Es idéntico al que se crea con el comando anterior.

[interfaz de línea de comandos de Azure]: http://azure.microsoft.com/downloads/

<!---HONumber=AcomDC_0224_2016-->