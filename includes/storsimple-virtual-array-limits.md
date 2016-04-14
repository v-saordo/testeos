
| **Identificador de límites** | **Límite** | **Comentarios** |
|-----------------------------------------------------------------------------------------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Capacidad total (incluida la nube) | Hasta 64 TB por dispositivo virtual |
| Número máximo de credenciales de la cuenta de almacenamiento por dispositivo | 1 | |
| Número máximo de volúmenes o recursos compartidos | 16 | |
| Tamaño mínimo del volumen o del recurso compartido en niveles | 500 GB | |
| Tamaño máximo del volumen o del recurso compartido en niveles | 20 TB | |
| Tamaño mínimo del volumen o del recurso compartido anclado localmente | 50 GB | |
| Tamaño máximo del volumen o del recurso compartido anclado localmente | 2 TB | |
| Número máximo de conexiones de iSCSI de iniciadores | 512 | |
| Número máximo de registros de control de acceso por dispositivo | 64 | |
| Número máximo de copias de seguridad retenidas por el dispositivo virtual en la carpeta *.backups* del servidor de archivos | 5 | Esto incluye las últimas copias de seguridad manuales y programadas (generadas por la directiva de copias de seguridad predeterminada). |
| Número máximo de copias de seguridad programadas retenidas por el dispositivo | 55 | 30 copias de seguridad diarias<br>12 copias de seguridad mensuales<br>13 copias de seguridad anuales |
| Número máximo de copias de seguridad manuales retenidas por el dispositivo | 45 | |
| Número máximo de volúmenes que se pueden procesar en paralelo para realizar una copia de seguridad o una restauración | 3 | Si hay más de 3 volúmenes, se procesarán secuencialmente a medida que las ranuras de procesamiento estén disponibles. |
| Tiempo de recuperación de la restauración | Restauración rápida | La restauración se basa en el mapa de calor y depende del tamaño del volumen.<br>Pueden llevarse a cabo operaciones de copia de seguridad mientras esté en curso una operación de restauración. |

<!---HONumber=AcomDC_0121_2016-->