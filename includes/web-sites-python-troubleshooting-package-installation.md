Algunos paquetes no pueden instalarse con pip cuando se ejecutan en Azure. Puede ser que el paquete no está disponible en el índice de paquetes de Python. Puede ser que sea necesario un compilador (un compilador no está disponible en el equipo que ejecuta la aplicación web en el Servicio de aplicaciones de Azure).

En esta sección, analizaremos algunas maneras de tratar este problema.

### Solicitar ruedas

Si la instalación del paquete requiere un compilador, debe intentar ponerse en contacto con el propietario del paquete para solicitar que se proporcionen ruedas para el paquete.

Con la disponibilidad reciente del compilador de [Microsoft Visual C++ para Python 2.7][], ahora es más fácil crear paquetes que tienen código nativo para Python 2.7.

### Compilar ruedas (necesita Windows)

Nota: al usar esta opción, asegúrese de que compila el paquete con un entorno de Python que coincide con la plataforma/arquitectura/versión que se usa en la aplicación web del Servicio de aplicaciones de Azure (Windows/32 bits/2.7 o 3.4).

Si el paquete no se instala porque requiere un compilador, puede instalar el compilador en el equipo local y generar una rueda para el paquete, que después incluirá en el repositorio.

Usuarios de Mac/Linux: si no tiene acceso a un equipo de Windows, consulte [Creación de una máquina virtual que ejecuta Windows][] para saber cómo crear una máquina virtual en Azure. Puede usarlo para generar las ruedas, agregarlas al repositorio y descartar la máquina virtual si lo desea.

En el caso de Python 2.7, puede instalar el [compilador de Microsoft Visual C++ para Python 2.7][].

En el caso de Python 3.4,puede instalar [Microsoft Visual C++ 2010 Express][].

Para compilar ruedas, necesitará el paquete de ruedas:

    env\scripts\pip install wheel

Usará `pip wheel` para compilar una dependencia:

    env\scripts\pip wheel azure==0.8.4

Esto crea un archivo .whl en la carpeta \\wheelhouse. Agregue la carpeta \\wheelhouse y los archivos de rueda al repositorio.

Edite requirements.txt para agregar la opción `--find-links` al principio. Esto indica a pip que busque una coincidencia exacta en la carpeta local antes de ir al índice de paquetes de Python.

    --find-links wheelhouse
    azure==0.8.4

Si desea incluir todas las dependencias en la carpeta \\wheelhouse y no utilizar el índice de paquetes Python, puede hacer que pip omita el índice de paquetes agregando `--no-index` en la parte superior de requirements.txt.

    --no-index

### Personalizar la instalación

Puede personalizar el script de implementación para instalar un paquete en el entorno virtual mediante un instalador alternativo, como easy\_install. Consulte deploy.cmd para obtener un ejemplo comentado. Asegúrese de que estos paquetes no aparezcan en requirements.txt, para evitar que pip los instale.

Agregue lo siguiente al script de implementación:

    env\scripts\easy_install somepackage

También es posible que pueda utilizar easy\_install para instalar desde un instalador exe (algunos son compatibles con zip, por lo que easy\_install los admite). Agregue el instalador al repositorio e invoque easy\_install pasando la ruta de acceso al archivo ejecutable.

Agregue lo siguiente al script de implementación:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### Incluir el entorno virtual en el repositorio (necesita Windows)

Nota: al usar esta opción, asegúrese de utilizar un entorno virtual que coincida con la plataforma/arquitectura/versión que se usa en la aplicación web del Servicio de aplicaciones (Windows/32 bits/2.7 o 3.4).

Si incluye el entorno virtual en el repositorio, puede impedir que el script de implementación que administre el entorno virtual en Azure mediante la creación de un archivo vacío:

    .skipPythonDeployment

Se recomienda eliminar el entorno virtual existente en la aplicación para evitar los archivos sobrantes que se generan cuando el entorno virtual se administra automáticamente.


[Creación de una máquina virtual que ejecuta Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
[Microsoft Visual C++ para Python 2.7]: http://aka.ms/vcpython27
[compilador de Microsoft Visual C++ para Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949

<!---HONumber=Oct15_HO3-->