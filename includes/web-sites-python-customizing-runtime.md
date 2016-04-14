Azure determinará la versión de Python que se usará para su entorno virtual con la siguiente prioridad:

1. versión especificada en runtime.txt en la carpeta raíz
1. versión especificada en la configuración de Python en la configuración de la aplicación web (la hoja **Configuración** > **Configuración de la aplicación** de su aplicación web en el portal de Azure Portal)
1. python-2.7 es el valor predeterminado si no se especifica ninguno de los anteriores

Los valores válidos para el contenido

    \runtime.txt

son:

- python-2.7
- python-3.4

Si se especifica la versión micro (tercer dígito), se ignora.

<!---HONumber=Oct15_HO3-->