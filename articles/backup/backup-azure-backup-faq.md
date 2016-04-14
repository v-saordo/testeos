<properties
   pageTitle="P+F de Copia de seguridad de Azure | Microsoft Azure"
   description="Respuestas a las preguntas más frecuentes sobre agente de copia de seguridad, copia de seguridad y retención, recuperación, seguridad y otras preguntas comunes sobre la solución de Copia de seguridad de Azure."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="solución de copia de seguridad; servicio de copia de seguridad"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="01/28/2016"
	 ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# P+F de servicio de Copia de seguridad de Azure
A continuación se muestra una lista de las preguntas más frecuentes acerca de la Copia de seguridad de Azure. Si tiene alguna pregunta adicional sobre la solución Copia de seguridad de Azure, vaya al [foro de discusión](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) y publique sus preguntas. Alguien de nuestra comunidad le ayudará a obtener respuestas. Si una pregunta es frecuente, se agregará a este artículo para que se pueda encontrar de forma rápida y sencilla.

## Instalación y configuración
**P1. ¿Qué es la lista de sistemas operativos compatibles desde la que puedo realizar copias de seguridad en Azure con la Copia de seguridad de Azure?** <br/> R1. Copia de seguridad de Azure admite lista de sistemas operativos siguiente


| Sistema operativo | Plataforma | SKU |
| :------------- |-------------| :-----|
| Windows 8 y SP más recientes | 64 bits | Enterprise, Pro |
| Windows 7 y SP más recientes | 64 bits | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 y SP más recientes | 64 bits | Enterprise, Pro |
| Windows 10 | 64 bits | Enterprise, Pro, Home |
|Windows Server 2012 R2 y SP más recientes|	64 bits|	Standard, Datacenter, Foundation|
|Windows Server 2012 y SP más recientes|	64 bits|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 y SP más recientes |64 bits|	Standard, Workgroup|
|Windows Storage Server 2012 y SP más recientes |64 bits |Standard, Workgroup
|Windows Server 2012 R2 y SP más recientes |64 bits|	Essential|
|Windows Server 2008 R2 SP1 |64 bits|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bits|	Standard, Enterprise, Datacenter, Foundation|

**P2. ¿Dónde puedo descargar el agente de Copia de seguridad de Azure más reciente?** <br/> R2. Puede descargar el último agente [aquí](http://aka.ms/azurebackup_agent). Se puede instalar en Windows Server, el cliente de Windows o el servidor SCDPM.

**P3. ¿Qué versión de servidor SCDPM es compatible?** <br/> R3. Se recomienda que instale el agente de Copia de seguridad de Azure [más reciente](http://aka.ms/azurebackup_agent) en el paquete acumulativo de actualizaciones de SCDPM más reciente (UR6 desde julio de 2015).

**P4. Al configurar al agente de Copia de seguridad de Azure, se me solicita que escriba las "credenciales de almacén". ¿Hay alguna fecha de caducidad asociada a las credenciales de almacén?** <br/> R4. Sí, la credencial de almacén caduca después de 48 horas. Si el archivo caduca, inicie sesión en el Portal de Azure y descargue los archivos de credenciales de almacén desde el almacén de copia de seguridad.

**P5. ¿Hay algún límite del número de almacenes de copia de seguridad que se pueden crear en cada suscripción de Azure?** <br/> R5. Sí. A partir de julio de 2015, puede crear 25 almacenes por suscripción. Si necesita más almacenes, cree una nueva suscripción.

**P6. ¿Debo considerar el almacén como una entidad de facturación?** <br/> R6. Aunque es posible obtener una factura detallada de cada almacén, es muy recomendable que considere una suscripción de Azure como entidad de facturación. Es coherente en todos los servicios y es más fácil de administrar.

**P7. ¿Hay algún límite en el número de servidores o máquinas que se puede registrar en cada almacén?** <br/> R7. Sí, puede registrar hasta 50 máquinas por almacén. Para las máquinas virtuales de IaaS de Azure, el límite es de 200 máquinas virtuales por almacén. Si necesita registrar más máquinas, cree un nuevo almacén.

**P8. ¿Hay algún límite en la cantidad de datos de los que se puede hacer una copia de seguridad desde un servidor o cliente de Windows o un servidor de SCDPM?** <br/> R8. Nº

**P9. ¿Cómo registro mi servidor en otro centro de datos?**<br/> R9. En general, los datos de copia de seguridad se envían al centro de datos del servicio de copia de seguridad en el que está registrado. La forma más sencilla de cambiar el centro de datos es desinstalar el agente y volver a instalarlo y registrarlo en un nuevo centro de datos.

**P10. ¿Qué ocurre si cambio el nombre de un servidor de Windows de cuyos datos se está realizando una copia de seguridad en Azure?** <br/> R10. Las copias de seguridad configuradas actualmente se detendrán. Tendrá que volver a registrar el servidor con el almacén de copia de seguridad y se considerará un nuevo servidor de servicios de recuperación, por lo que la primera operación de copia de seguridad que se produce después del registro será una copia de seguridad completa de todos los datos incluidos en la copia de seguridad, en lugar de solo los cambios desde la última copia de seguridad. Sin embargo, si necesita realizar una operación de recuperación, puede recuperar los datos de los que se ha hecho copia de seguridad mediante la recuperación desde otra opción de recuperación de servidores.

**P11. ¿Desde qué tipos de unidades puedo realizar copias de seguridad de archivos y carpetas?** <br/> R11. La copia de seguridad no se puede realizar del siguiente conjunto de unidades/volúmenes de disco:

- Medios extraíbles: la unidad debe ser fija para poder usarse como origen de copia de seguridad.
- Volúmenes de solo lectura: el volumen debe ser grabable para que el servicio de copia de instantáneas de volumen (VSS) funcione.
- Volúmenes sin conexión: el volumen debe estar en línea para que VSS funcione.
- Recurso compartido de red: el volumen debe ser local en el servidor para que la copia de seguridad se realice en línea.
- Volúmenes protegidos por BitLocker: el volumen debe desbloquearse antes de que se pueda crear la copia de seguridad.
- Identificación del sistema de archivos: NTFS es el único sistema de archivos admitido por esta versión del servicio de copia de seguridad en línea.

**P12. ¿De qué tipos de archivos y carpetas puedo hacer copias de seguridad desde mi servidor?**<br/> R12. Se admiten los siguientes tipos:

- Cifrados
- Comprimidos
- Dispersos
- Comprimidos + dispersos
- Vínculos físicos: no compatibles, se omiten
- Puntos de repetición: no compatibles, se omiten
- Cifrados + comprimidos: no compatibles, se omiten
- Cifrados + dispersos: no compatibles, se omiten
- Secuencias comprimidas: no compatibles, se omiten
- Secuencias dispersas: no compatibles, se omiten

**P13. ¿Cuál es el requisito de tamaño mínimo para la carpeta de caché?** <br/> R13. El tamaño de la carpeta de caché se determina por la cantidad de datos de la que se realiza la copia de seguridad. En general, debe esperar que 5 % del espacio necesario para el almacenamiento de datos se asigne a la carpeta de caché.

**P14. ¿Cómo puedo aislar datos específicos del servidor para que no los recuperen otros servidores de mi organización?**<br/> R14. Los servidores que se registran con el mismo almacén podrán recuperar los datos de copia de seguridad desde otros servidores que usan la misma frase de contraseña. Si tiene servidores de los que desea asegurarse de que la recuperación solo se realiza en servidores específicos de su organización, debe usar una frase de contraseña independiente designada para esos servidores. Por ejemplo, los servidores de recursos humanos podrían usar una frase de contraseña de cifrado, los servidores de contabilidad, otra y los servidores de almacenamiento, otra distinta.

**P15. ¿Puedo "migrar" mis datos de copia de seguridad entre suscripciones?** <br/> R15: No.

**P16. ¿Puedo "migrar" mi almacén de copia de seguridad de una suscripción a otra?** <br/> R16. No. El almacén se crea en un nivel de suscripción y no se puede reasignar a otra suscripción una vez que se crea.

**P17. ¿Funciona el agente de Copia de seguridad de Azure en un servidor que usa la desduplicación de Windows Server 2012?** <br/>A17: Sí. El servicio del agente convierte los datos desduplicados en datos normales cuando prepara la operación de copia de seguridad. A continuación, optimiza los datos para la copia de seguridad, los cifra y los envía al servicio de copia de seguridad en línea.

**P18. ¿Se eliminan los datos de copia de seguridad si cancelo una copia de seguridad después de que se inicie?** <br/> R18: No. El almacén de copia de seguridad almacena la copia de seguridad de los datos que se transfieren hasta el momento de la cancelación. Copia de seguridad de Azure usa un mecanismo de punto de comprobación para que los datos de copia de seguridad se comprueben en un punto ocasionalmente durante la copia de seguridad y el siguiente proceso de copia de seguridad pueda validar la integridad de los archivos. La siguiente copia de seguridad desencadenada sería incremental respecto a los datos de los que se haya creado una copia de seguridad anteriormente. Esto permite usar mejor el ancho de banda, por lo que no es necesario transferir los mismos datos reiteradamente.

**P19. ¿Por qué aparece la advertencia "Las copias de seguridad de Azure no se han configurado para este servidor" aunque programé previamente copias de seguridad periódicas?** <br/>R19: Esto puede ocurrir cuando la configuración de la programación de copia de seguridad almacenada en el servidor local no es la misma que la configuración almacenada en el almacén de copia de seguridad. Cuando el servidor o la configuración se recuperan a un estado válido conocido, las programaciones de copia de seguridad pueden perder la sincronización. Si esto sucede, debe volver a configurar la directiva de copia de seguridad y elegir **Ejecutar copia de seguridad ahora** para volver a sincronizar el servidor local con Azure.

**P20. ¿Cuáles son las reglas de firewall que hay que configurar para copias de seguridad de Copia de seguridad de Azure?** <br/>R20. Asegúrese de que las reglas de firewall permiten la comunicación con las direcciones URL siguientes para copias de seguridad sin problemas de local a Azure y protección de carga de trabajo en Azure:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

**P21. ¿Puedo instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure cuya copia de seguridad ya la ha realizado el servicio Copia de seguridad de Azure mediante la extensión Vm?** <br/> R21. Totalmente. El servicio Copia de seguridad de Azure proporciona copia de seguridad del nivel de máquina virtual para las máquinas virtuales de Azure mediante la extensión VM y puede instalar el agente de Copia de seguridad de Azure en el SO invitado de Windows para proteger archivos y carpetas en un SO invitado.

**P22. ¿Puedo instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure para realizar la copia de seguridad de los archivos y carpetas que hay en un almacenamiento temporal proporcionado por la máquina virtual de Azure?** <br/> R22. Puede instalar al agente de Copia de seguridad de Azure en el SO invitado de Windows y realizar la copia de seguridad de los archivos y carpetas del almacenamiento temporal. Sin embargo, tenga en cuenta que las copias de seguridad empezarán a provocar errores cuando se borren los datos del almacenamiento temporal. Además, durante la restauración solo podrá restaurar en un almacenamiento no temporal si se han eliminado los datos del almacenamiento temporal.


## Copia de seguridad y retención
**P1. ¿Hay algún límite en el tamaño de cada origen de datos del que se realiza una copia de seguridad?** <br/> R1. A partir de agosto de 2015, el tamaño máximo del origen de datos es el que se menciona a continuación para varios sistemas operativos

|S.No |	Sistema operativos |	Tamaño máximo del origen de datos |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 o superior| 54400 GB|
|2| Windows 8 o superior| 54400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1700 GB|
|4| Windows 7 | 1700 GB|

El tamaño de origen de datos se mide según se menciona a continuación

|	Origen de datos |	Detalles |
| :-------------: |:-------------|
|Volumen |La cantidad de datos de los que se realiza copia de seguridad desde un único volumen de un equipo. Esto es aplicable para los volúmenes protegidos en el servidor y los equipos cliente.|
|Máquina virtual de Hyper-V|Suma de los datos de todos los discos duros virtuales de la máquina virtual de los que se hace copia de seguridad|
|Base de datos de Microsoft SQL Server|Tamaño de una sola base de datos SQL de la que se hace copia de seguridad |
|Microsoft SharePoint|Suma de las bases de datos de contenido y configuración dentro de una granja de SharePoint de los que se hace copia de seguridad|
|Microsoft Exchange|Suma de todas las bases de datos de un servidor de Exchange de las que se hace copia de seguridad|
|Estado del sistema y BMR|Cada copia individual del estado del sistema o BMR del equipo del que se hace copia de seguridad|

**P2. ¿Hay algún límite en el número de veces que se puede programar la copia de seguridad al día?**<br/> R2. Sí, la Copia de seguridad de Azure permite realizar 3 copias de seguridad diarias a través del servidor/cliente de Windows, 2 copias de seguridad diarias a través de SCDPM y una copia al día para las máquinas virtuales de IaaS.

**P3. ¿Hay alguna diferencia entre la directiva de programación de copia de seguridad de DPM y la de Azure (es decir, en Windows Server sin DPM)?** <br/> R3. Sí. Con DPM, puede especificar la programación diaria, semanal, mensual y anual, mientras que desde un servidor de Windows (sin DPM), puede especificar solo programaciones diarias o semanales.

**P4. ¿Hay alguna diferencia entre la directiva de retención de DPM y la de Copia de seguridad de Azure (es decir, en Windows Server sin DPM)?**<br/> R4. No, tienen las mismas capacidades. Puede especificar directivas de retención diaria, semanal, mensual y anual.

**P5. ¿Puedo configurar de forma selectiva mis directivas de retención (es decir, configurar semanal y diariamente, pero no anual y mensualmente)?**<br/> R5. Sí, la estructura de retención de la Copia de seguridad de Azure permite tener una flexibilidad completa en la definición de la directiva de retención según sus requisitos.

**P6. ¿Puedo "programar una copia de seguridad" a las 6 p.m. y especificar "directivas de retención" en un momento diferente?**<br/> R6. No. Las directivas de retención pueden aplicarse solo en puntos de copia de seguridad. En la imagen siguiente, la directiva de retención se especifica en las copias de seguridad realizadas a las 12:00 y las 18:00 horas. <br/>

![Programar copia de seguridad y retención](./media/backup-azure-backup-faq/Schedule.png) <br/>

**P7. ¿Se transfiere una copia incremental para las directivas de retención programadas?** <br/> R7. No, la copia incremental se envía en el momento mencionado en la página de programación de copia de seguridad. Los puntos que se pueden retener se determinan según la directiva de retención.

**P8. Si se conserva la copia de seguridad durante un período prolongado, ¿se tarda mucho tiempo en recuperar los datos (por ejemplo ,el punto más antiguo)?** <br/> R8. No, el tiempo necesario para la recuperación del último punto o del punto más antiguo es el mismo. Cada punto de recuperación se comporta como un punto completo.

**P9. Si cada punto de recuperación es como un punto completo, ¿afecta esto al almacenamiento de copia de seguridad facturable total?**<br/> R9. Los productos de retención a largo plazo típicos almacenan los datos de copia de seguridad como puntos completos. Sin embargo, son ineficaces en cuanto a almacenamiento, pero resultan más fáciles y rápidos de restaurar. Las copias incrementales se almacenan con eficacia pero requieren que restaure una cadena de datos que influye en el tiempo de recuperación. La arquitectura de almacenamiento único de la Copia de seguridad de Azure ofrece lo mejor de ambas opciones, al almacenar datos de forma óptima para restauraciones más rápidas e incurrir en costes de almacenamiento bajo. Este enfoque le asegura que su ancho de banda (de entrada y salida) se use de forma eficaz, el almacenamiento se conserve al mínimo y el tiempo dedicado a la recuperación se reduzca al mínimo.

**P10. ¿Hay un límite en el número de puntos de recuperación que se pueden crear?**<br/> R10. No. Hemos eliminado los límites a los puntos de recuperación. Puede crear tantos puntos de recuperación como desee.

**P11. ¿Por qué la cantidad de datos transferida en la copia de seguridad es distinta a la cantidad de datos de los que realizo la copia de seguridad?**<br/> R11. Todos los datos de los que se realiza una copia de seguridad se comprimen y se cifran antes de ser transferidos. Puede esperar unas ventajas de compresión de entre el 30 y el 40 % en función del tipo de datos de los cuales se realiza la copia de seguridad.

## Recuperación
**P1. ¿Cuántas recuperaciones puedo realizar en los datos cuya copia de seguridad se crea en Azure?**<br/> R1. No hay ningún límite en cuanto al número de recuperaciones de la Copia de seguridad de Azure.

**P2. ¿Tengo que pagar por el tráfico de salida desde el centro de datos de Azure durante las recuperaciones?**<br/> R2. No. Sus recuperaciones son gratuitas y no se cobra por el tráfico de salida.

## Seguridad
**P1. ¿Se cifran los datos que se envían a Azure?** <br/> R1. Sí. Los datos se cifran en la máquina cliente/servidor/SCDPM local mediante AES256 y se envían a través de un vínculo HTTPS seguro.

**P2. ¿También se cifran los datos de copia de seguridad en Azure?**<br/> R2. Sí. Los datos que se envían a Azure permanecen cifrados (en reposo). Microsoft no descifra los datos de copia de seguridad en ningún momento.

**P3. ¿Cuál es la longitud mínima de la clave de cifrado utilizada para cifrar los datos de copia de seguridad?** <br/> R3. La clave de cifrado debe tener al menos 16 caracteres.

**P4. ¿Qué sucede si pierdo la clave de cifrado? ¿Puedo recuperar los datos (o) puede Microsoft recuperar los datos?** <br/> R4. La clave utilizada para cifrar los datos de copia de seguridad está presente en las instalaciones del cliente. Microsoft no mantiene una copia en Azure y no tiene acceso a la clave. Si el cliente pierde la clave, Microsoft no puede recuperar los datos de copia de seguridad.

## Memoria caché de copia de seguridad

**P1. ¿Cómo puedo cambiar la ubicación de la memoria caché especificada para el agente de Copia de seguridad de Azure?**

+ Para detener OBEngine, ejecute el siguiente comando en un símbolo del sistema con privilegios elevados:

  ```PS C:\> Net stop obengine```

+ Copie la carpeta de que el espacio de la memoria caché a otra unidad con espacio suficiente. Se recomienda copiar los archivos de la carpeta de espacio de memoria caché, en lugar de moverlos; después de confirmar que las copias de seguridad funcionan con el nuevo espacio de memoria caché se puede quitar el espacio de memoria caché original.

+ Actualice las siguientes entradas del Registro con la ruta de acceso a la nueva carpeta del espacio de memoria caché:


	| Ruta de acceso del Registro | Clave del Registro | Valor |
	| ------ | ------- | ------ |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | <i>Nueva ubicación de la carpeta de la memoria caché</i> |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | <i>Nueva ubicación de la carpeta de la memoria caché</i> |


+ Para iniciar OBEngine, ejecute el siguiente comando en un símbolo del sistema con privilegios elevados:

  ```PS C:\> Net start obengine```

Una vez que las copias de seguridad se realizan correctamente con la nueva ubicación de caché, puede quitar la carpeta de la memoria caché original.

<!---HONumber=AcomDC_0218_2016-->