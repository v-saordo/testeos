<properties
   pageTitle="SQL Azure con Azure RemoteApp | Microsoft Azure"
   description="Aprenda a usar SQL Azure con Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="02/02/2016"
   ms.author="elizapo"/>

# SQL Azure con Azure RemoteApp

A menudo, cuando los clientes eligen hospedar sus aplicaciones de Windows en la nube con Azure RemoteApp también desean migrar sus datos como servidores SQL Server en la nube para una implementación de nube completa. Esto permite que cualquier dispositivo en cualquier lugar pueda acceder a la solución hospedada en la nube completa en cualquier momento con Azure RemoteApp. A continuación se muestran vínculos y referencias junto con instrucciones para ayudarle con este proceso.

## Migración de los datos SQL

Comience con [Migración de una base de datos de SQL Server a una Base de datos SQL de Azure en la nube](../sql-database/sql-database-cloud-migrate.md).

## Configuración de Azure RemoteApp
Hospede la aplicación de Windows en Azure RemoteApp. A continuación se muestran unas instrucciones paso a paso de un nivel muy alto:

1.     Cree la [máquina virtual de la plantilla de Azure RemoteApp](remoteapp-imageoptions.md). 
2.     Instale la aplicación necesaria en la máquina virtual.
3.     Configure la aplicación para que se conecte a Base de datos SQL y confirme que funciona.
4.     Ejecute sysprep y apague la máquina virtual. Captúrelo como una imagen para usarlo con Azure. **Nota:** debe asegurarse que la aplicación puede conservar la información de conectividad de la base de datos mediante el proceso sysprep. Si la aplicación no puede conservar la información de conexión a la base de datos, puede hacer participar al proveedor de la aplicación para comprobar cómo podemos especificar la cadena de conexión.
5.     Importe la imagen personalizada a la biblioteca de Azure RemoteApp mediante la selección de la ubicación geográfica apropiada en la que reside su implementación de SQL Azure. 
6.     Implemente una colección RemoteApp en el mismo centro de datos que la implementación de SQL Azure mediante la plantilla anterior y publique la aplicación. La implementación de Azure RemoteApp en el mismo centro de datos que la implementación de SQL Azure ayuda a garantizar la mayor velocidad de conexión y reducir la latencia. 

## Consideraciones de configuración de RemoteApp y SQL:
Hay unos cuantos puntos a tener en cuenta al usar Azure SQL con RemoteApp:

Aprenda [cómo configurar un firewall de Base de datos SQL de Azure](../sql-database/sql-database-firewall-configure.md). En el artículo se indica lo siguiente: "Inicialmente, todo el acceso a su servidor de Base de datos SQL de Azure está bloqueado por el firewall. Para comenzar a usar el servidor de Base de datos SQL de Azure, debe ir al Portal clásico y especificar una o más reglas de firewall de nivel de servidor que permitan el acceso al servidor de Base de datos SQL de Azure. Use las reglas de firewall para especificar qué intervalos de direcciones IP de Internet se permiten y si las aplicaciones de Azure pueden intentar conectarse o no al servidor de Base de datos SQL de Azure”.

Además, cuando un equipo intenta conectarse al servidor de bases de datos desde Internet, el firewall comprueba la dirección IP de origen de la solicitud con el conjunto completo de nivel de servidor y (en caso necesario) las reglas de firewall de nivel de base de datos: “Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión se concede al servidor de Base de datos SQL de Azure”. Por lo tanto, podemos usar intervalos de direcciones IP, no solo direcciones IP de origen individuales.

Siga las instrucciones paso a paso que aparecen en [Configuración del firewall en la Base de datos SQL mediante el Portal de Azure](../sql-database/sql-database-configure-firewall-settings.md) para especificar el intervalo de direcciones IP. Al configurar las reglas de firewall de SQL, proporcione el intervalo de direcciones IP de la subred especificado para la colección Azure RemoteApp. Esto debería permitir que los servidores de ARA se conecten a Base de datos SQL, aunque tendrán direcciones IP asignadas dinámicamente.

## Solución de problemas
Si el uso de una aplicación cliente hospedada en Azure RemoteApp que se conecta a una instancia de Base de datos SQL hospedada en Azure o local es lento, podría haber varias razones.

- La latencia de red del dispositivo a Azure es alta. Cambie a la conexión de red mejor y más rápida que pueda para mejorar el rendimiento. Use [azurespeed.com](http://azurespeed.com/) como una herramienta general para probar la latencia de los dispositivos al centro de datos de Azure.  
- La aplicación cliente hospedada en Azure RemoteApp está bajo presión. Seleccionar un plan de facturación diferente, como la facturación Premium, mejorará el rendimiento. Otro truco es supervisar los recursos que consume la aplicación: durante una sesión activa, pulse la secuencia de teclas ctrl-alt-fin, que iniciará la pantalla SAS, seleccione Administrador de tareas y observe el uso de recursos para la aplicación.
- SQL Server está bajo presión o no está optimizado. Siga las instrucciones de SQL para la solucionar el problema. 

<!---HONumber=AcomDC_0211_2016-->