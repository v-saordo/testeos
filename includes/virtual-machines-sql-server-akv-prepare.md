## Preparación para la integración de AKV
Para usar la Integración de Almacén de claves de Azure para configurar la máquina virtual de SQL Server, hay varios requisitos previos:

1.	[Instalar Azure Powershell](#install-azure-powershell)
2.	[Crear un directorio de Azure Active Directory](#create-an-azure-active-directory)
3.	[Creación de un Almacén de claves](#create-a-key-vault)

Las secciones siguientes describen estos requisitos previos y la información que necesita recopilar para ejecutar más adelante los cmdlets de PowerShell.

### Azure PowerShell
Asegúrese de que ha instalado el SDK más reciente de Azure PowerShell. Para más información, vea [Instalación y configuración de Azure PowerShell](../articles/powershell-install-configure.md).

### Creación de un directorio de Azure Active Directory
En primer lugar, tiene que tener un [directorio de Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) en la suscripción. Entre otras muchas ventajas, esto le permite conceder permiso para Almacén de claves a determinados usuarios y aplicaciones.

A continuación, registre una aplicación con AAD. Esto le dará una cuenta de entidad de servicio que tenga acceso a su Almacén de claves, algo que la máquina virtual va a necesitar. En el artículo sobre Almacén de claves de Azure, puede encontrar estos pasos en la sección [Registro de una aplicación con Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register), o bien puede ver los pasos con capturas de pantalla en la sección sobre **obtener una identidad para la aplicación** de [esta entrada de blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Antes de completar estos pasos, tenga en cuenta que durante este registro tiene que recopilar la siguiente información que necesitará más adelante cuando habilite la integración de Almacén de claves de Azure en la máquina virtual de SQL.

- Después de agregar la aplicación, busque el **IDENTIFICADOR DE CLIENTE** en la pestaña de **CONFIGURACIÓN**. ![ID de cliente de Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
	
	El id. de cliente se asigna posteriormente al parámetro **$spName** (nombre de entidad de servicio) en el script de PowerShell para habilitar la integración de Almacén de claves de Azure. 
- Además, durante estos pasos al crear la clave, copie el secreto de la clave tal como se muestra en la captura de pantalla siguiente. Este secreto de clave se asigna posteriormente al parámetro **$spSecret** (secreto de entidad de servicio) en el script de PowerShell. ![Secreto de Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
- Tiene que autorizar este nuevo id. de cliente para que tenga los siguientes permisos de acceso: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign**, y **verify**. Esto se hace con el cmdlet [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx). Para más información, vea [Autorización de la aplicación para que use la clave o el secreto](../articles/key-vault/key-vault-get-started.md#authorize).

### Creación de un Almacén de claves
Para poder usar Almacén de claves de Azure para guardar las claves que se utilizarán para el cifrado en la máquina virtual, tiene que tener acceso a un Almacén de claves. Si no ha configurado ya su Almacén de claves, cree uno siguiendo los pasos que se mencionan en el tema [Introducción al Almacén de claves de Azure](../articles/key-vault/key-vault-get-started.md). Antes de completar estos pasos, tenga en cuenta que durante esta configuración tiene que recopilar la alguna información que necesitará más adelante cuando habilite la integración de Almacén de claves de Azure en la máquina virtual de SQL.

Cuando llegue al paso Creación de un Almacén de claves, tenga en cuenta la propiedad **vaultUri** devuelta, que es la dirección URL del Almacén de claves. En el ejemplo ofrecido en este paso, que se muestra a continuación, el nombre del Almacén de claves es ContosoKeyVault, por lo tanto, su dirección URL sería https://contosokeyvault.vault.azure.net/.

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

La URL del Almacén de claves se asigna posteriormente al parámetro **$akvURL** en el script de PowerShell para habilitar la integración de Almacén de claves de Azure.

<!---HONumber=AcomDC_1223_2015-->