Recurso|Gratuito|Compartido (Vista previa)|Básica|Estándar|Premium (vista previa)</th>
---|---|---|---|---|---
[Aplicaciones web, móviles o de API](../services/app-service/) por [plan de Servicio de aplicaciones](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>
[Aplicaciones lógicas](../services/app-service/) por [plan de Servicio de aplicaciones](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup>|10|10|10|20 por núcleo|20 por núcleo
[Plan del Servicio de aplicaciones](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 por región|10 por grupo de recursos|10 por grupo de recursos|10 por grupo de recursos|10 por grupo de recursos
Tipo de instancia de proceso|Compartido|Compartido|Dedicado<sup>3</sup>|Dedicado<sup>3</sup>|Dedicado<sup>3</sup></p>
[Escalado horizontal](../articles/app-service-web/web-sites-scale.md) (instancias máximas)|1 compartido|1 compartido|3 dedicados<sup>3</sup>|10 dedicados<sup>3</sup>|50 dedicados<sup>3,4</sup>
Almacenamiento<sup>5</sup>|1 GB<sup>5</sup>|1 GB<sup>5</sup>|10 GB<sup>5</sup>|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
Tiempo de CPU (día)<sup>6</sup>|60 minutos|240 minutos|Sin límite, se paga según las [tarifas](../pricing/details/app-service/)</a> estándar|Sin límite, se paga según las tarifas estándar|Sin límite, se paga según las tarifas estándar
Memoria (1 hora)|1\.024 MB por plan de Servicio de aplicaciones|1\.024 MB por aplicación|N/D|N/D|N/D
Ancho de banda|165 MB|Ilimitado; se aplican [tasas por transferencia de datos](../pricing/details/data-transfers/)|Ilimitado; se aplican tasas por transferencia de datos.|Ilimitado; se aplican tasas por transferencia de datos.|Ilimitado; se aplican tasas por transferencia de datos.
Arquitectura de la aplicación|32 bits|32 bits|32 bits/64 bits|32 bits/64 bits|32 bits/64 bits
Web Sockets por instancia<sup>7</sup>|5|35|350|Sin límite|Sin límite
Conexiones de depurador [simultáneas](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) por aplicación|1|1|1|5|5
[subdominio de azurewebsites.net con FTP/S y SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
[Compatibilidad con dominio](../articles/app-service-web/web-sites-custom-domain-name.md) personalizado||X|X|X|X
[Compatibilidad con SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md) del dominio personalizado|||Sin límite|Sin límite, 5 conexiones SSL SNI y 1 conexión SSL de IP incluidas|Sin límite, 5 conexiones SSL SNI y 1 conexión SSL de IP incluidas
Equilibrador de carga integrado||X|X|X|X
[Siempre activado](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[Copias de seguridad programadas](../articles/app-service-web/web-sites-backup.md)||||Una vez al día|Una vez cada 5 minutos<sup>8</sup>
[Escala automática](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md)<sup>9</sup>|X|X|X|X|X
[Compatibilidad con Programador de Azure](../services/scheduler/)||X|X|X|X
[Supervisión de extremos](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[Ranuras de ensayo (vista previa)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
Dominios personalizados por aplicación</a>||500|500|500|500
Contrato de nivel de servicio||<p>|99,9 %|99,95%<sup>10</sup>|99,95%<sup>10</sup>

<sup>1</sup>Las aplicaciones y las cuotas de almacenamiento son por plan de Servicio de aplicaciones, a menos que se indique lo contrario. <sup>2</sup>El número real de aplicaciones que puedes hospedar en estas máquinas depende de la actividad de las aplicaciones, el tamaño de las instancias de máquina y el correspondiente uso de los recursos. <sup>3</sup>Las instancias dedicadas pueden tener diferentes tamaños. Consulte [Precios de Servicio de aplicaciones](../pricing/details/app-service/) para obtener más detalles. Hay instancias adicionales disponibles mediante la apertura de una solicitud de soporte. <sup>4</sup>El nivel Premium permite hasta 50 instancias de proceso (en función de la disponibilidad) y 500 GB de espacio en disco cuando se usa en entornos de Servicio de aplicaciones, y 20 instancias de proceso y 250 GB de almacenamiento, en caso contrario. <sup>5</sup>El límite de almacenamiento es el tamaño total del contenido en todas las aplicaciones en el mismo plan de Servicio de aplicaciones. Se pueden aumentar los límites de almacenamiento mediante la apertura de una solicitud de soporte. <sup>6</sup>Estos recursos están limitados por los recursos físicos en las instancias dedicadas (el tamaño de la instancia y el número de instancias). <sup>7</sup>Si se escala una aplicación en el nivel Básico a dos instancias, dispones de 350 conexiones simultáneas para cada una de las dos. <sup>8</sup>El nivel Premium permite intervalos de copia de seguridad de hasta cada 5 minutos cuando se usa en entornos de Servicio de aplicaciones y 50 veces al día, de lo contrario. <sup>9</sup>Ejecuta scripts o ejecutables personalizados a petición, según una programación o de manera continua como tarea en segundo plano dentro de tu instancia de Servicio de aplicaciones. Siempre disponible se requiere para la ejecución continua de Trabajos web. Se requiere el Programador de Azure de nivel Gratis o Estándar para Trabajos web programados. No hay ningún límite predefinido en el número de WebJobs que puede ejecutar en una instancia de Servicio de aplicaciones, pero sí hay límites prácticos que dependen de lo que el código de la aplicación esté intentando hacer. <sup>10</sup>El acuerdo de nivel de servicio (SLA) del 99,95 % proporcionado para implementaciones que usen varias instancias con el Administrador de tráfico de Azure configurado para conmutación por error.

<!---HONumber=AcomDC_0218_2016-->