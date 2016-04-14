### Creación de un extremo TCP para la máquina virtual

Para poder acceder a SQL Server desde Internet, la máquina virtual debe tener un extremo para escuchar la comunicación TCP de entrada. Este paso de la configuración de Azure dirige el tráfico del puerto TCP de entrada a un puerto TCP al que puede tener acceso la máquina virtual.

>[AZURE.NOTE]Si se va a conectar en el mismo servicio en la nube o red virtual, no es necesario crear un extremo accesible públicamente. En ese caso, puede continuar con el paso siguiente. Para obtener más información, consulte: [Escenarios de conexión](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. En el Portal de administración de Azure, haga clic en **MÁQUINAS VIRTUALES**.
	
2. Haga clic en la máquina virtual recién creada. Se muestra información acerca de su máquina virtual.
	
3. Cerca de la parte superior de la página, seleccione la página **EXTREMOS** y, a continuación, en la parte inferior de la página haga clic en **AGREGAR**.
	
4. En la página **Agregar extremo a máquina virtual**, haga clic en **Agregar extremo independiente** y, a continuación, en la flecha de avance para continuar.
	
5. En la página **Especifique los detalles del extremo**, proporcione la siguiente información.

	- En el cuadro **NOMBRE**, escriba un nombre para el extremo.
	- En el cuadro **PROTOCOLO**, seleccione **TCP**. De manera similar, puede escribir **57500** en el cuadro **PUERTO PÚBLICO**. Del mismo modo, puede escribir el puerto de escucha predeterminado de SQL Server **1433** en el cuadro **Puerto privado**. Observe que muchas organizaciones seleccionan números de puerto distintos para evitar ataques malintencionados a la seguridad. 

6. Haga clic en la marca de verificación para continuar. Se crea el extremo.

<!---HONumber=AcomDC_0107_2016-->