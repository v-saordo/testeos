Existen dos formas distintas de instalar los módulos: la Galería de PowerShell y el Instalador de plataforma web. El resultado final es prácticamente el mismo, aunque el modo en que decida cómo se realiza la instalación determinará dónde se instalan los módulos en el equipo de forma predeterminada.

Cuando instala desde la Galería de PowerShell, los archivos estarán de forma predeterminada en *%ProgramFiles%\\WindowsPowerShell\\Modules*. Cuando instala desde el Instalador de plataforma web, los archivos se encontrarán de forma predeterminada en *%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell*. Por este motivo, deberá seguir una de ellas con el fin de evitar errores al actualizar los cmdlets en el futuro. El Instalador de plataforma web recibirá cmdlets actualizados mensualmente. La Galería recibe versiones actualizadas de los cmdlets en el momento en que se publican. Por ese motivo, algunos usuarios prefieren utilizar la Galería.

Para obtener más información sobre cómo configurar Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](../articles/powershell-install-configure.md).

**Instalación de módulos desde la Galería de PowerShell**

1. Para instalar el módulo del Administrador de recursos directamente desde la Galería, abra Windows PowerShell como administrador y escriba lo siguiente:

		Install-Module AzureRM
		Install-AzureRM

2. Una vez instalados los módulos, debe importarlos para poder utilizarlos:

		Import-AzureRM

**Instalación de módulos mediante el Instalador de plataforma web**

- Puede instalar módulos mediante el [Instalador de plataforma web](http://aka.ms/webpi-azps). Al hacer clic en el vínculo, se iniciará el instalador.

- Si se producen errores al utilizar el Instalador de plataforma web, pueden deberse a que ya ha instalado una versión anterior de los cmdlets mediante la Galería. Consulte esta [entrada de blog](https://azure.microsoft.com/blog/azps-1-0/), que puede ayudarle a quitar versiones anteriores de los módulos y a estar de nuevo operativo. Suelen producirse errores cuando se ha utilizado el Instalador de plataforma web y se cambia a la Galería o viceversa. Al quitar los módulos que se instalaron anteriormente, se resuelve este problema; después, puede instalar desde la nueva ubicación.

<!---HONumber=AcomDC_0218_2016-->