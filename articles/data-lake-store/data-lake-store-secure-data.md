<properties 
   pageTitle="Protección de los datos almacenados en el Almacén de Azure Data Lake | Azure" 
   description="Aprenda a proteger los datos del almacén de Azure Data Lake mediante grupos y listas de control de acceso" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="nitinme"/>

# Protección de los datos almacenados en el Almacén de Azure Data Lake

Para proteger los datos en el Almacén de Azure Data Lake, se adopta un enfoque de tres pasos.

1. Comience creando grupos de seguridad en Azure Active Directory (AAD). Estos grupos de seguridad se usan para implementar el control de acceso basado en roles (RBAC) en el Portal de Azure. Para obtener más información, consulte [Control de acceso basado en roles de Azure Active Directory](role-based-access-control-configure.md).

2. Asigne los grupos de seguridad de AAD a la cuenta de Almacén de Azure Data Lake. Esto controla el acceso a la cuenta de Almacén de Data Lake desde el portal y las operaciones de administración desde el portal o las API.

3. Asigne los grupos de seguridad de AAD como listas de control de acceso (ACL) en el sistema de archivos del Almacén de Data Lake.

En este artículo se proporcionan instrucciones sobre cómo usar el Portal de Azure para realizar las tareas anteriores.

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md).

## Creación de grupos de seguridad en Azure Active Directory

Para obtener instrucciones sobre cómo crear grupos de seguridad de AAD y cómo agregar usuarios al grupo, consulte [Administración de grupos de seguridad en Azure Active Directory](active-directory-accessmanagement-manage-groups.md).

## Asignación de grupos de seguridad o usuarios a cuentas de Almacén de Azure Data Lake

Cuando asigna usuarios o grupos de seguridad a cuentas de Almacén de Azure Data Lake, controla el acceso a las operaciones de administración en la cuenta mediante el Portal de Azure y las API del Administrador de recursos de Azure.

1. Abra una cuenta de Almacén de Azure Data Lake. En el panel izquierdo, haga clic en **Examinar** y en **Almacén de Data Lake**; después, en la hoja Almacén de Data Lake, haga clic en el nombre de la cuenta a la que desee asignar un usuario o un grupo de seguridad.

2. En la hoja de su cuenta de Almacén de Data Lake, haga clic en el icono de usuarios.

	![Asignación de un grupo de seguridad a la cuenta de Almacén de Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Asignación de un grupo de seguridad a la cuenta de Almacén de Azure Data Lake")

3. La hoja **Usuarios** indica de forma predeterminada el grupo **Administradores de suscripciones** como propietario.

	![Agregar usuarios y roles](./media/data-lake-store-secure-data/adl.add.group.roles.png "Agregar usuarios y roles")
 
	Existen dos maneras de agregar un grupo y asignar roles pertinentes.

	* Agregue un usuario o grupo a la cuenta y después asigne un rol o
	* Agregue un rol y después asigne usuarios o grupos al rol.

	En esta sección, nos centramos en el primer enfoque: agregar un grupo y después asignar roles. Puede realizar pasos similares para seleccionar primero un rol y después asignar grupos a ese rol.
	
4. En la hoja **Usuarios**, haga clic en **Agregar** para abrir la hoja **Agregar acceso**. En la hoja **Agregar acceso**, haga clic en **Seleccionar un rol** y después seleccione un rol para el usuario o grupo.

	 ![Agregar un rol para el usuario](./media/data-lake-store-secure-data/adl.add.user.1.png "Agregar un rol para el usuario")

	Los roles **Propietario** y **Colaborador** proporcionan acceso a diversas funciones de administración en la cuenta de Data Lake. Para aquellos usuarios que interactúan con datos en Data Lake, puede agregarlos al rol **Lector**. El ámbito de estos roles se limita a las operaciones de administración relacionadas con la cuenta de Almacén de Azure Data Lake.

	Para las operaciones de datos, los permisos individuales del sistema de archivos definen lo que los usuarios pueden hacer. Por lo tanto, un usuario con el rol Lector solamente ve la configuración administrativa asociada a la cuenta pero potencialmente puede leer y escribir datos en función de los permisos del sistema de archivos que tengan asignados. Los permisos del sistema de archivos del Almacén de Data Lake se describen en [Asignación de usuarios o grupos de seguridad como ACL al sistema de archivos del Almacén de Azure Data Lake](#filepermissions).



5. En la hoja **Agregar acceso**, haga clic en **Agregar usuarios** para abrir la hoja **Agregar usuarios**. En esta hoja, busque el grupo de seguridad que creó antes en Azure Active Directory. Si tiene muchos grupos en los que buscar, use el cuadro de texto en la parte superior para filtrar según el nombre del grupo. Haga clic en **Seleccionar**.

	![Agregar un grupo de seguridad](./media/data-lake-store-secure-data/adl.add.user.2.png "Agregar un grupo de seguridad")

	Si desea agregar un grupo o usuario que no aparece, para invitarles, use el icono **Invitar** y especifique la dirección de correo electrónico para el usuario o grupo.

6. Haga clic en **Aceptar**. Debería ver el grupo de seguridad que se agregó como se muestra a continuación.

	![Grupo de seguridad agregado](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de seguridad agregado")

7. Ahora el usuario o el grupo de seguridad tienen acceso a la cuenta de Almacén de Azure Data Lake. Si desea proporcionar acceso a usuarios específicos, puede agregarlos al grupo de seguridad. De forma similar, si desea revocar el acceso de un usuario, puede quitarle del grupo de seguridad. También puede asignar varios grupos de seguridad a una cuenta.

## <a name="filepermissions"></a>Asignación de usuarios o grupos de seguridad como ACL al sistema de archivos del Almacén de Azure Data Lake

Al asignar usuarios o grupos de seguridad al sistema de archivos de Azure Data Lake, establece el control de acceso sobre los datos almacenados en el Almacén de Azure Data Lake. En la versión actual, puede establecer las ACL solo en el nodo raíz del sistema de archivos.

1. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Explorador de datos**.

	![Creación de directorios en la cuenta de Almacén de Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Crear directorios en la cuenta de Data Lake")

2. En la hoja **Explorador de datos**, haga clic en la raíz de su cuenta y después, en la hoja de la cuenta, haga clic en el icono **Acceso**.

	![Establecer las ACL en el sistema de archivos de Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Establecer las ACL en el sistema de archivos de Data Lake")

3. La hoja **Acceso** enumera el acceso estándar y el acceso personalizado ya asignados a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado.

	![Mostrar acceso estándar y personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "Mostrar acceso estándar y personalizado")

	* El acceso estándar es el acceso de estilo UNIX, donde se especifican lectura, escritura y ejecución (rwx) para tres clases de usuario distintas: propietario, grupo y otros.
	* El acceso personalizado corresponde a las ACL de POSIX y permite establecer permisos para usuarios o grupos designados específicos y no solo para el propietario o el grupo del archivo.
	
	Para obtener más información, consulte la página sobre las [ACL de HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists).

4. Haga clic en el icono **Agregar** para abrir la hoja **Agregar acceso personalizado**. En esta hoja, haga clic en **Seleccionar usuario o grupo** y después, en la hoja **Seleccionar usuario o grupo**, busque el grupo de seguridad que creó antes en Azure Active Directory. Si tiene muchos grupos en los que buscar, use el cuadro de texto en la parte superior para filtrar según el nombre del grupo. Haga clic en el grupo que desee agregar y después en **Seleccionar**.

	![Agregar un grupo](./media/data-lake-store-secure-data/adl.acl.3.png "Agregar un grupo")

5. Haga clic en **Seleccionar permisos**, seleccione los permisos que desea asignar al grupo y después haga clic en **Aceptar**.

	![Asignar permisos al grupo](./media/data-lake-store-secure-data/adl.acl.4.png "Asignar permisos al grupo")


	>[AZURE.NOTE] Se necesita el permiso Ejecución para la enumeración de directorios y, con frecuencia, cuando se proporciona acceso de solo lectura a los datos a un usuario o grupo.


6. En la hoja **Agregar acceso personalizado**, haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, se mostrará ahora en la hoja **Acceso**.

	![Asignar permisos al grupo](./media/data-lake-store-secure-data/adl.acl.5.png "Asignar permisos al grupo")

	> [AZURE.IMPORTANT] En la versión actual, solo puede tener 9 entradas en **Acceso personalizado**. Si desea agregar más de 9 usuarios, debe crear grupos de seguridad, agregar usuarios a los grupos de seguridad y proporcionar acceso a esos grupos de seguridad para la cuenta de Almacén de Data Lake.

7. Si es necesario, también puede modificar los permisos de acceso después de agregar el grupo. Active o desactive la casilla para cada tipo de permiso (lectura, escritura, ejecución) en función de si desea quitarlo o asignarlo al grupo de seguridad. Haga clic en **Guardar** para guardar los cambios o en **Descartar** para deshacerlos.

## Quitar grupos de seguridad de una cuenta de Almacén de Azure Data Lake

Cuando quita usuarios o grupos de seguridad de cuentas de Almacén de Azure Data Lake, solamente está cambiando el acceso a las operaciones de administración en la cuenta mediante el Portal de Azure y las API del Administrador de recursos de Azure.

1. En la hoja de su cuenta de Almacén de Data Lake, haga clic en el icono de usuarios.

	![Asignar un grupo de seguridad a la cuenta de Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Asignar un grupo de seguridad a la cuenta de Azure Data Lake")

2. En la hoja **Usuarios**, haga clic en el grupo de seguridad que desea quitar.

	![Grupo de seguridad para quitar](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de seguridad para quitar")

3. En la hoja del grupo de seguridad, haga clic en **Quitar**.

	![Grupo de seguridad quitado](./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de seguridad quitado")

## Quitar las ACL de grupo de seguridad del sistema de archivos del Almacén de Azure Data Lake

Cuando quita las ACL de grupos de seguridad del sistema de archivos del Almacén de Azure Data Lake, está cambiando el acceso a los datos del Almacén de Data Lake.

1. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Explorador de datos**.

	![Crear directorios en la cuenta de Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Crear directorios en la cuenta de Data Lake")

2. En la hoja **Explorador de datos**, haga clic en la raíz de su cuenta y después, en la hoja de la cuenta, haga clic en el icono **Acceso**.

	![Establecer las ACL en el sistema de archivos de Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Establecer las ACL en el sistema de archivos de Data Lake")

3. En la hoja **Acceso**, en la sección **Acceso personalizado**, haga clic en el grupo de seguridad que desee quitar. En la hoja **Acceso personalizado**, haga clic en **Quitar** y después en **Aceptar**.

	![Asignar permisos al grupo](./media/data-lake-store-secure-data/adl.remove.acl.png "Asignar permisos al grupo")


## Consulte también

- [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
- [Copiar datos de los blobs de almacenamiento de Azure en el Almacén Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introducción al Almacén de Azure Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
- [Introducción al Almacén de Azure Data Lake mediante .NET SDK](data-lake-store-get-started-net-sdk.md)

<!---HONumber=AcomDC_0218_2016-->