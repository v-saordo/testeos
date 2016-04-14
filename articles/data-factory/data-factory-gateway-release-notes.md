<properties 
	pageTitle="Notas de la versión de Data Management Gateway | Factoría de datos de Azure" 
	description="Notas de la versión de Data Management Gateway" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Notas de la versión de Data Management Gateway

Uno de los desafíos de la integración de datos moderna es mover datos sin problemas entre ubicación la local y la nube. Factoría de datos hace que dicha integración sea perfecta con Data Management Gateway, que es un agente que se puede instalar de forma local para permitir el movimiento de datos híbridos.

Para más información, consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

## Versión actual (1.10.5892.1)

• Mejoras del rendimiento • Corrección de errores

## Versiones anteriores

## 1\.9.5865.2

- Funcionalidad de actualización automática sin intervención del usuario
- Icono de bandeja de nuevo con indicadores de estado de la puerta de enlace
- Capacidad para "Actualizar ahora" desde el cliente
- Capacidad para establecer la hora de programación de las actualizaciones
- Script de PowerShell para activar y desactivar la actualización automática 
- Mejoras en el rendimiento
- Corrección de errores

## 1\.8.5822.1

- Mejora de la solución de problemas
- Mejoras en el rendimiento
- Corrección de errores

### 1\.7.5795.1

- Mejoras en el rendimiento
- Corrección de errores

### 1\.7.5764.1

- Mejoras en el rendimiento
- Corrección de errores

### 1\.6.5735.1

- Compatibilidad origen y receptor HDFS locales
- Mejoras en el rendimiento
- Corrección de errores

### 1\.6.5696.1

- Mejoras en el rendimiento
- Corrección de errores

### 1\.6.5676.1

- Compatibilidad con herramientas de diagnóstico de Administrador de configuración
- Columnas de la tabla de soporte técnico para orígenes de datos tabulares de la Factoría de datos de Azure
- Compatibilidad con SQL DW para Factoría de datos de Azure
- Compatibilidad con Reclusive en BlobSource y FileSource para Factoría de datos de Azure
- Compatibilidad con CopyBehavior - MergeFiles, PreserveHierarchy y FlattenHierarchy en BlobSink y FileSink con copia binaria para Factoría de datos de Azure
- Compatibilidad con los informes de progreso de la actividad de copia para Factoría de datos de Azure
- Compatibilidad con la validación de conectividad de origen de datos para Factoría de datos de Azure
- Corrección de errores


### 1\.6.5672.1

- Compatibilidad con nombre de tabla de origen de datos ODBC para Factoría de datos de Azure
- Mejoras en el rendimiento
- Corrección de errores

### 1\.6.5658.1

- Compatibilidad con receptor de archivos para Factoría de datos de Azure
- Compatibilidad con la conservación jerarquía en copia binaria para Factoría de datos de Azure
- Compatibilidad con idempotencia de la actividad de copia para Factoría de datos de Azure
- Corrección de errores

### 1\.6.5640.1

- Compatibilidad con tres orígenes de datos más para Factoría de datos de Azure (ODBC, OData y HDFS)
- Compatibilidad con carácter de comillas en el analizador de archivos .csv para Factoría de datos de Azure
- Compatibilidad con compresión (BZip2)
- Corrección de errores

### 1\.5.5612.1

- Compatibilidad con cinco bases de datos relacionales para Factoría de datos de Azure (MySQL, PostgreSQL, DB2, Teradata y Sybase)
- Compatibilidad de compresión (Gzip y Deflate)
- Mejoras en el rendimiento
- Corrección de errores


### 1\.4.5549.1

- Incorporación de compatibilidad de origen de datos Oracle para Factoría de datos de Azure
- Mejoras en el rendimiento
- Corrección de errores

### 1\.4.5492.1

- Binario unificado que admite los servicios Factoría de datos de Microsoft Azure y Office 365 Power BI
- Restricción de la interfaz de usuario de configuración y del proceso de registro
- Factoría de datos de Azure: compatibilidad de entrada y salida de Azure con origen de datos SQL Server

### 1\.2.5303.1

- 	Corrección del problema del tiempo de espera para admitir más conexiones de orígenes de datos que requieren mucho tiempo. 
 	
### 1\.1.5526.8

- Requiere .NET Framework 4.5.1 como requisito previo durante la instalación.

### 1\.0.5144.2

- No hay cambios que afecten a los escenarios de Factoría de datos de Azure. 

## Preguntas y respuestas

### ¿Por qué intenta conectarse el Administrador de origen de datos a una puerta de enlace?
Se trata de un diseño de seguridad en el que solo se pueden configurar los orígenes de datos locales para el acceso a la nube dentro de la red corporativa y sus credenciales no fluirán fuera del firewall corporativo. Asegúrese de que su equipo puede acceder el equipo en que está instalado la puerta de enlace.

<!---HONumber=AcomDC_0302_2016-->