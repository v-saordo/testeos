Azure PowerShell está disponible en dos versiones: 1.0 y 0.9.8. Si ya tiene scripts y no desea cambiarlos ahora, puede continuar usando la versión 0.9.8. Al usar la versión 1.0, debe probar cuidadosamente los scripts en los entornos de preproducción antes de usarlos en producción para evitar efectos inesperados.

Los cmdlets de 1.0 siguen el patrón de nomenclatura {verbo}-AzureRm{nombre}; en el que, los nombres de 0.9.8 no incluyen **Rm** (por ejemplo, New-AzureRmResourceGroup en lugar de New-AzureResourceGroup). Al usar Azure PowerShell 0.9.8, primero debe habilitar el modo de Administrador de recursos mediante la ejecución del comando **Switch-AzureMode AzureResourceManager**. Este comando no es necesario en la versión 1.0.

Las nuevas características se agregarán solo a la versión 1.0. Para obtener información acerca de la versión 1.0, así como de su instalación y desinstalación, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

<!---HONumber=AcomDC_1203_2015-->