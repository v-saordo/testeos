<properties
	pageTitle="Planeación de la infraestructura de copia de seguridad de máquinas virtuales en Azure | Microsoft Azure"
	description="Consideraciones importantes al planear la realización de copias de seguridad de máquinas virtuales en Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="copias de seguridad de máquinas virtuales, realizar copias de seguridad de máquinas virtuales"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="trinadhk; jimpark; markgal;"/>

# Planeación de la infraestructura de copia de seguridad de máquinas virtuales en Azure
En este artículo se tratan los conceptos clave que hay que tener en cuenta al planear la realización de copias de seguridad de máquinas virtuales en Azure. Si ha [preparado el entorno](backup-azure-vms-prepare.md), este es el paso siguiente antes de comenzar a realizar la [copia de seguridad de las máquinas virtuales](backup-azure-vms.md). Si necesita más información sobre máquinas virtuales de Azure, vea la [Documentación sobre máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/).

## ¿Cómo realiza Azure la copia de seguridad de las máquinas virtuales?
El servicio Copia de seguridad de Azure inicia el trabajo de copia de seguridad a la hora programada y desencadena la extensión de copia de seguridad para tomar una instantánea de un momento en el tiempo. Esta instantánea se toma en coordinación con el Servicio de instantáneas de volumen (VSS) para obtener una instantánea coherente de los discos de la máquina virtual sin tener que apagarla.

Después de tomar la instantánea, el servicio Copia de seguridad de Azure transfiere los datos al almacén de copia de seguridad. Para que el proceso de copia de seguridad resulte más eficaz, el servicio identifica y transfiere únicamente los bloques de datos que han cambiado desde la última copia de seguridad.

![Arquitectura de copia de seguridad de máquinas virtuales de Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Cuando finaliza la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación.

### Coherencia de datos
La copia de seguridad y restauración de datos críticos para el negocio resulta complicado por el hecho de que es necesario realizar la copia de seguridad mientras se ejecutan las aplicaciones que producen los datos. Para solucionar este problema, el servicio Copia de seguridad de Azure proporciona copias de seguridad coherentes con la aplicación para las cargas de trabajo de Microsoft utilizando VSS para garantizar que los datos se escriben correctamente en el almacenamiento.

>[AZURE.NOTE] En las máquinas virtuales Linux, solo se pueden realizar copias de seguridad coherentes con el archivo, dado que Linux no dispone de una plataforma equivalente a VSS.

Copia de seguridad de Azure realiza copias de seguridad completas de VSS en máquinas virtuales de Windows (más información sobre [copia de seguridad completa de VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Para habilitar las copias de seguridad de VSS, hay que establecer la siguiente clave del Registro en la máquina virtual.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


En esta tabla se describen los tipos de coherencia y las condiciones en las que se producen durante los procedimientos de copia de seguridad y restauración de máquinas virtuales de Azure.

| Coherencia | Con base en VSS | Explicación y detalles |
|-------------|-----------|---------|
| Coherencia de las aplicaciones | Sí | Se trata del tipo de coherencia ideal para las cargas de trabajo de Microsoft ya que garantiza que:<ol><li> la máquina virtual *arranca*. <li>No hay *daños*. <li>No hay *pérdida de datos*.<li> Los datos son coherentes con la aplicación que usa los datos, implicando a la aplicación en el momento de realizar la copia de seguridad, mediante VSS.</ol> La mayoría de cargas de trabajo de Microsoft tienen escritores VSS que realizan acciones específicas de carga de trabajo relacionadas con la coherencia de los datos. Por ejemplo, Microsoft SQL Server tiene un escritor VSS que garantiza que las escrituras en el archivo de registro de transacciones y en la base de datos se realizan correctamente.<br><br> En las copias de seguridad de máquina virtual de Azure, la obtención de un punto de recuperación coherente con la aplicación significa que la extensión de copia de seguridad pudo invocar el flujo de trabajo VSS y completarlo *correctamente* antes de que se tomase la instantánea de la máquina virtual. Naturalmente, esto significa que también se han invocado los escritores VSS de todas las aplicaciones de la máquina virtual de Azure.<br><br>(Aprenda los [conceptos básicos de VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) y profundice en los detalles de su [funcionamiento](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). |
| Coherencia del sistema de archivos | Sí, para equipos basados en Windows | Hay dos escenarios donde el punto de recuperación puede ser *coherente con el sistema de archivos*:<ul><li>Copias de seguridad de máquinas virtuales Linux en Azure, ya que Linux no tiene una plataforma equivalente a VSS.<li>Error de VSS durante la copia de seguridad de máquinas virtuales de Windows en Azure.</li></ul> En ambos casos, lo mejor que se puede hacer es asegurarse de que: <ol><li> La máquina virtual *arranca*. <li>No hay *daños*.<li>No hay *pérdida de datos*.</ol> Las aplicaciones deben implementar su propio mecanismo de "reparación" en los datos restaurados.|
| Coherencia de bloqueos | No | Esta situación es equivalente a aquellos casos en que una máquina experimenta un "bloqueo" (a través de un restablecimiento parcial o completo). Esto suele ocurrir cuando la máquina virtual de Azure se apaga en el momento de realizar la copia de seguridad. En las copias de seguridad de máquina virtual de Azure, la obtención de un punto de recuperación coherente con el bloqueo significa que Copia de seguridad de Azure no ofrece ninguna garantía sobre la coherencia de los datos en el medio de almacenamiento, ni desde la perspectiva del sistema operativo ni desde la perspectiva de la aplicación. Solamente se capturan y se hace una copia de seguridad de los datos que ya existen en el disco en el momento de la copia de seguridad. <br/> <br/> Aunque no hay ninguna garantía, en la mayoría de los casos el sistema operativo se iniciará. Normalmente, esto va seguido de un procedimiento de comprobación de disco como chkdsk para corregir los errores por daños. Se perderán los datos o las escrituras en memoria que no se hayan vaciado completamente en el disco. Normalmente, la aplicación sigue con su propio mecanismo de comprobación en caso de que se deba realizar una reversión de datos. <br><br>Por ejemplo, si el registro de transacciones tiene entradas que no están presentes en la base de datos, el software de la base de datos realiza una reversión hasta que los datos sean coherentes. Cuando los datos se reparten en varios discos virtuales (por ejemplo, volúmenes distribuidos), un punto de recuperación coherente con el bloqueo no ofrece ninguna garantía sobre la corrección de los datos.|


## Rendimiento y uso de recursos
Al igual que el software de copia de seguridad que se implementa de forma local, la copia de seguridad de máquinas virtuales en Azure también tiene que planearse según el uso de recursos y la capacidad. Los [límites de Almacenamiento de Azure](azure-subscription-service-limits.md#storage-limits) definirán cómo se estructuran las implementaciones de máquinas virtuales para obtener un rendimiento máximo con un impacto mínimo en las cargas de trabajo en ejecución.

Hay dos límites de Almacenamiento de Azure principales que afectan al rendimiento de la copia de seguridad:

- Salida máxima por cuenta de almacenamiento
- Tasa de solicitud total por cuenta de almacenamiento

### Límites de la cuenta de almacenamiento
Cuando los datos de copia de seguridad se copian de la cuenta de almacenamiento del cliente, se tienen en cuenta las métricas de operaciones de entrada y salida por segundo (IOPS) y de salida (rendimiento de almacenamiento) de la cuenta de almacenamiento. Al mismo tiempo, las máquinas virtuales también ejecutan y consumen IOPS y capacidad de proceso. El objetivo es asegurarse de que el tráfico total (copia de seguridad y máquina virtual) no supera los límites de la cuenta de almacenamiento.

### Número de discos
El proceso de copia de seguridad es expansivo e intenta consumir tantos recursos como sea posible, con el fin de completar la copia de seguridad lo más rápido posible. Pero todas las operaciones de E/S están limitadas por la *capacidad de proceso de destino de un blob*, que tiene un límite de 60 MB por segundo. Para acelerar el proceso de copia de seguridad, se intenta realizar la copia de seguridad de cada disco de la máquina virtual *en paralelo*. Por lo tanto, si una máquina virtual tiene cuatro discos, el servicio Copia de seguridad de Azure intentará hacer copia de seguridad de los cuatro discos en paralelo. Por ello, el factor más importante que determina el tráfico de copia de seguridad que sale de una cuenta de almacenamiento de cliente es el **número de discos** cuya copia de seguridad se realiza desde la cuenta de almacenamiento.

### Programación de copia de seguridad
Un factor secundario que afecta al rendimiento es la **programación de copia de seguridad**. Si configura todas las máquinas virtuales para realizar la copia de seguridad al mismo tiempo, el número de discos cuya copia de seguridad se realiza *en paralelo* aumentará, ya que el servicio Copia de seguridad de Azure intentará realizar la copia de seguridad de tantos discos como sea posible. Por tanto, una forma de reducir el tráfico de copia de seguridad de una cuenta de almacenamiento es asegurarse de que se realizan copias de seguridad de diferentes máquinas virtuales en distintos momentos del día, sin superponerse.

## Planificación de capacidad
Reunir todos estos factores significa que el uso de cuentas de almacenamiento debe planearse correctamente. Descargue la [hoja cálculo de Excel de planeación de la capacidad de copia de seguridad de máquinas virtuales](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver el impacto de las opciones de programación de las copias de seguridad y los discos.

### Rendimiento de la copia de seguridad
Para cada disco cuya copia de seguridad se realiza, Copia de seguridad de Azure lee los bloques en el disco y solo almacena los datos cambiados (copia de seguridad incremental). En esta tabla se muestran los valores de rendimiento medios que puede esperar del servicio Copia de seguridad de Azure. Con ella, puede calcular la cantidad de tiempo que tarda en realizarse una copia de seguridad de un disco de un tamaño determinado.

| Operación de copia de seguridad | Mejor rendimiento |
| ---------------- | ---------- |
| Copia de seguridad inicial | 160 Mbps |
| Copia de seguridad incremental (DR) | 640 Mbps <br><br> Este rendimiento puede reducirse considerablemente si hay mucha renovación de código dispersa en el disco del que es necesario hacer una copia de seguridad |

### Tiempo total de copia de seguridad de máquinas virtuales
Aunque la mayoría del tiempo de copia de seguridad se dedica a leer y copiar los datos, hay otras operaciones que contribuyen al tiempo total necesario para la copia de seguridad de una máquina virtual:

- Tiempo necesario para [instalar o actualizar la extensión de copia de seguridad](backup-azure-vms.md#offline-vms)
- Hora de la instantánea: tiempo dedicado a desencadenar una instantánea. Las instantáneas se desencadenan cerca de la hora de copia de seguridad programada.
- Tiempo de espera en la cola. Puesto que el servicio Copia de seguridad procesa las copias de seguridad de varios clientes, la operación de copia de datos de copia de seguridad de la instantánea al almacén de copia de seguridad de Azure podría no iniciarse inmediatamente. En los momentos de carga máxima, los tiempos de espera pueden ampliarse hasta 8 horas debido al número de copias de seguridad que se procesan. Sin embargo, el tiempo total de la copia de seguridad de máquina virtual será de menos de 24 horas para las directivas de copia de seguridad diarias.

## Cifrado de datos

Copia de seguridad de Azure no cifra los datos como parte del proceso de copia de seguridad. Pero se pueden cifrar datos dentro de la máquina virtual y los datos protegidos de copia de seguridad sin ningún problema (vea más información sobre [copia de seguridad de datos cifrados](backup-azure-vms-encryption.md)).


## ¿Cómo se calculan las instancias protegidas?
Las máquinas virtuales de Azure cuya copia de seguridad se realiza mediante el servicio Copia de seguridad de Azure estarán sujetas a los [precios de Copia de seguridad de Azure](https://azure.microsoft.com/pricing/details/backup/). El cálculo de instancias protegidas se basa en el tamaño *real* de la máquina virtual, que es la suma de todos los datos de la máquina virtual, excepto el "disco de recursos".

*No* se le facturará en función del tamaño máximo admitido para cada disco de datos conectado a la máquina virtual, sino de los datos reales almacenados en el disco de datos. De forma similar, la factura de almacenamiento de copia de seguridad se basa en la cantidad de datos almacenados con Copia de seguridad de Azure, que es la suma de los datos reales de cada punto de recuperación.

Por ejemplo, veamos una máquina virtual de tamaño estándar A2 con dos discos de datos adicionales con un tamaño máximo de 1 TB cada uno. La tabla siguiente proporciona los datos almacenados en cada uno de estos discos:

|Tipo de disco|Tamaño máximo|Datos reales presentes|
|---------|--------|------|
| Disco del sistema operativo | 1023 GB | 17 GB |
| Disco local o disco de recursos | 135 GB | 5 GB (no incluidos en la copia de seguridad) |
| Disco de datos 1 |	1023 GB | 30 GB |
| Disco de datos 2 | 1023 GB | 0 GB |

El tamaño *real* en este caso es el tamaño de la máquina virtual de 17 GB + 30 GB + 0 GB = 47 GB. Esto se convierte en el tamaño de instancia protegida en el que se basa la factura mensual. A medida que crece la cantidad de datos en la máquina virtual, el tamaño de instancia protegida usado para la facturación también cambiará en consecuencia.

La facturación no se inicia hasta que se completa la primera copia de seguridad correcta. En este punto, se iniciará la facturación del almacenamiento y las instancias protegidas. La facturación continúa mientras haya *datos de cualquier copia de seguridad almacenados con Copia de seguridad de Azure* para la máquina virtual. Al realizar la operación de suspender la protección no se detiene la facturación si se conservan los datos de copia de seguridad.

La facturación de una máquina virtual especificada se suspenderá solo si se ha detenido la protección *y* se eliminan los datos de copia de seguridad. Cuando no hay ningún trabajo de copia de seguridad activo (cuando se ha detenido la protección), el tamaño de la máquina virtual en el momento de la última copia de seguridad correcta se convierte en el tamaño de instancia protegida en el que se basa la factura mensual.

## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## Pasos siguientes

- [Copia de seguridad de máquinas virtuales](backup-azure-vms.md)
- [Administrar copia de seguridad de máquina virtual](backup-azure-manage-vms.md)
- [Restauración de máquinas virtuales](backup-azure-restore-vms.md)
- [Solución de problemas de copia de seguridad de máquinas virtuales](backup-azure-vms-troubleshoot.md)

<!---HONumber=AcomDC_0302_2016-->