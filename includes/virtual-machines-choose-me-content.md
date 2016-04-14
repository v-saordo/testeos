| Opciones de proceso | Público |
| ------------------ | --------   |
| [Servicio de aplicaciones] | Aplicaciones web, aplicaciones móviles, aplicaciones de API y aplicaciones lógicas escalables para cualquier dispositivo |
| [Servicios en la nube] | Aplicaciones en la nube de n niveles escalables y altamente disponibles con mayor control del sistema operativo |
| [Máquinas virtuales] | Máquinas virtuales Linux y Windows personalizadas con un control completo del sistema operativo |

<a name="tellmevm"></a>
## Información sobre las máquinas virtuales en Azure

Máquinas virtuales de Azure permite crear y utilizar máquinas virtuales en la nube. Al proporcionar lo que se conoce como *Infraestructura como servicio (IaaS)*, la tecnología de máquina virtual se puede usar de varias formas. A continuación, se indican algunos ejemplos:

- **Máquinas virtuales para desarrollo y prueba.** Los grupos de desarrollo suelen usar máquinas virtuales porque ofrecen una manera rápida y fácil de crear un equipo con configuraciones específicas necesarias para codificar y probar una aplicación. Máquinas virtuales de Azure proporciona una manera económica y sencilla de crear estas máquinas virtuales, utilizarlas y suprimirlas cuando dejan de ser necesarias.
- **Aplicaciones en ejecución en la nube.** Para algunas aplicaciones, la ejecución en la nube pública tiene sentido en términos económicos. Por ejemplo, en aplicaciones con grandes picos de demanda. Siempre es posible comprar máquinas suficientes para que el propio centro de datos se ocupe de estos picos, pero estas máquinas probablemente estarán infrautilizadas durante gran parte del tiempo. La ejecución de esta aplicación en Azure permite pagar por máquinas virtuales adicionales solo cuando son necesarias y apagarlas cuando dejan de serlo. Imagine también que es una empresa nueva que necesita recursos informáticos a petición rápidamente y sin compromiso. Nuevamente Azure puede ser la opción correcta.
- **Extensión de su centro de datos propio a la nube pública.** Con Red virtual de Azure, su organización puede crear una red virtual (VNET) que sirve como extensión de su propia red local y agregar máquinas virtuales a dicha red. Esto permite ejecutar aplicaciones como [SharePoint](virtual-machines-sharepoint-infrastructure-services.md), [SQL Server](virtual-machines-sql-server-infrastructure-services.md) y otras en una máquina virtual de Azure. Este enfoque puede resultar más fácil de implementar o menos costoso que su ejecución en máquinas virtuales de su propio centro de datos.   
- **Recuperación ante desastres.** En lugar de pagar continuamente por un centro de datos de copia de seguridad que pocas veces se utiliza, la recuperación de desastres basada en Infraestructura como servicio le permite pagar por los recursos informáticos que necesita solo cuando realmente los necesita. Por ejemplo, si se produce un error en su centro de datos principal, puede crear máquinas virtuales que se ejecutan en Azure para ejecutar aplicaciones esenciales y luego apagarlas cuando ya no sean necesarias.

Al igual que otras máquinas virtuales, una máquina virtual en Azure tiene un sistema operativo, capacidades de almacenamiento y de redes, y puede ejecutar una amplia gama de aplicaciones. Se puede utilizar una imagen proporcionada por Azure o uno de sus socios, o bien una propia. Los ejemplos incluyen distintas versiones, ediciones y configuraciones de:
 
-	Windows Server 
-	Servidores Linux como Suse, Ubuntu y CentOS
-	SQL Server
-	BizTalk Server 
-	SharePoint Server

Las máquinas virtuales utilizan discos duros virtuales (VHD) para almacenar el sistema operativo y datos. Estos discos también se usan para las imágenes entre las que se puede elegir para instalar un sistema operativo. Esto se puede ver en la figura siguiente, así como dos de las herramientas para crear y administrar las máquinas virtuales.

<a name="fig_createvms"></a> ![vm\_diagram](./media/virtual-machines-choose-me-content/diagram.png)

**Figura: Máquinas virtuales de Azure proporciona infraestructura como servicio.**

Las máquinas virtuales pueden administrarse mediante un portal basado en explorador, herramientas de línea de comandos con compatibilidad para scripts o directamente a través de la API de REST. Asociados de Microsoft, como RightScale y ScaleXtreme, también proporcionan servicios de administración que se basan en la API de REST.

Además del sistema operativo, las máquinas virtuales ofrecen otras opciones de configuración entre las que se incluyen las siguientes:

- El tamaño, que determina factores como el número de discos que se pueden adjuntar y la capacidad de procesamiento. Azure ofrece una amplia variedad de tamaños para admitir muchos tipos de usos. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).  
- La región de Azure donde se hospedará la nueva máquina virtual; por ejemplo, en Estados Unidos, Europa o Asia. 
- Extensiones de máquina virtual, que ofrecen capacidades adicionales para la máquina virtual, como la ejecución de antivirus o el uso de la característica de configuración de estado deseado de Windows PowerShell.

Entre otras ventajas que deben tenerse en cuenta con respecto a las máquinas virtuales se incluyen las siguientes:

**Pago por uso**: Azure cobra un precio por hora basado en el tamaño y el sistema operativo de la máquina virtual. En las fracciones de hora, solo se cobra por los minutos de uso. El precio del almacenamiento se calcula y se cobra por separado. Para obtener más información, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

**Resistencia**: Azure supervisa el hardware físico donde se hospeda cada máquina virtual en ejecución. Si se produce un error en un servidor físico que ejecuta una máquina virtual, Azure lo detecta, mueve la máquina virtual a un nuevo hardware y reinicia la máquina virtual. En ocasiones este proceso se denomina recuperación del servicio. Azure también protege los datos de una máquina virtual, ya que mantiene copias redundantes de los discos duros virtuales en el almacenamiento de blobs.

<!---HONumber=AcomDC_0128_2016-->