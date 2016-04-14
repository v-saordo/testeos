<properties 
	pageTitle="Análisis de transmisiones: Giro de las credenciales de inicio de sesión para entradas y salidas | Microsoft Azure" 
	description="Obtenga información acerca de cómo actualizar las credenciales para las entradas y salidas de Análisis de transmisiones."
	keywords="credenciales de inicio de sesión"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>

#Giro de las credenciales de inicio de sesión para entradas y salidas de los trabajos de Análisis de transmisiones

##Descripción breve
En la actualidad, Análisis de transmisiones de Azure no permite reemplazar las credenciales en una entrada/salida mientras se ejecuta el trabajo.

Aunque Análisis de transmisiones de Azure permite reanudar un trabajo desde la última salida, queríamos compartir todo el proceso para minimizar el retraso entre la detención y el inicio del trabajo y el giro de credenciales.

##Parte 1: preparación del nuevo conjunto de credenciales:
Esta parte es aplicable a las siguientes entradas/salidas:

* Almacenamiento de blobs
* Centros de eventos
* Base de datos SQL
* Almacenamiento de tablas

Para otras entradas/salidas, vaya a la Parte 2.

###Almacenamiento de blobs/almacenamiento de tablas
1.  Vaya a la extensión de almacenamiento en el Portal de administración de Azure: ![graphic1][graphic1]
2.  Busque el almacenamiento utilizado por su trabajo y vaya a él: ![graphic2][graphic2]
3.  Haga clic en el comando Administrar claves de acceso: ![graphic3][graphic3]
4.  Entre la clave de acceso principal y la clave de acceso secundaria, **elija la que no utilice su trabajo**.
5.  Presione regenerar: ![graphic4][graphic4]
6.  Copie la clave recién generada: ![graphic5][graphic5]
7.  Vaya a la parte 2.

###Centros de eventos
1.  Vaya a la extensión del bus de servicio en el Portal de administración de Azure: ![graphic6][graphic6]
2.  Busque el espacio de nombres del Bus de servicio utilizado por su trabajo y vaya a él: ![graphic7][graphic7]
3.  Si su trabajo usa una directiva de acceso compartido en el espacio de nombres del Bus de servicio, vaya al paso 6  
4.  Vaya a la Pestaña Centros de eventos: ![graphic8][graphic8]
5.  Busque el centro de eventos utilizado por su trabajo y vaya a él: ![graphic9][graphic9]
6.  Vaya a la pestaña Configurar: ![graphic10][graphic10]
7.  En la lista desplegable Nombre de directiva, busque la directiva de acceso compartido utilizada por su trabajo: ![graphic11][graphic11]
8.  Entre la clave principal y la clave secundaria, **elija la que no utilice su trabajo**.  
9.  Presione regenerar: ![graphic12][graphic12]
10. Copie la clave recién generada: ![graphic13][graphic13]
11. Vaya a la parte 2.  

###Base de datos SQL

>[AZURE.NOTE] Nota: deberá conectarse al servicio de base de datos SQL. Vamos a mostrar cómo hacerlo con la experiencia de administración en el Portal de administración de Azure, pero también puede utilizar alguna herramienta de cliente como SQL Server Management Studio.

1.  Vaya a la extensión de bases de datos SQL en el Portal de administración de Azure: ![graphic14][graphic14]
2.  Busque la base de datos SQL utilizada por su trabajo y **haga clic en el vínculo del servidor** en la misma línea: ![graphic15][graphic15]
3.  Haga clic en el comando Administrar: ![graphic16][graphic16]
4.  Tipo de base de datos maestra: ![graphic17][graphic17]
5.  Escriba su nombre de usuario y contraseña y haga clic en Iniciar sesión: ![graphic18][graphic18]
6.  Haga clic en Nueva consulta: ![graphic19][graphic19]
7.  Escriba la siguiente consulta reemplazando <login_name> por su nombre de usuario y <enterStrongPasswordHere> por la nueva contraseña: `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Haga clic en Ejecutar: ![graphic20][graphic20]
9.  Vuelva al paso 2 y esta vez haga clic en la base de datos: ![graphic21][graphic21]
10. Haga clic en el comando Administrar: ![graphic22][graphic22]
11. escriba su nombre de usuario y contraseña y haga clic en Iniciar sesión: ![graphic23][graphic23]
12. Haga clic en Nueva consulta: ![graphic24][graphic24]
13. Escriba la siguiente consulta reemplazando <user_name> por un nombre por el que desee identificar este inicio de sesión en el contexto de esta base de datos (puede proporcionar el mismo valor que asignó para <login_name>, por ejemplo) y reemplazar <login_name> por su nuevo nombre de usuario: `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Haga clic en Ejecutar: ![graphic25][graphic25]
15. Ahora debe proporcionar el nuevo usuario con los mismos roles y privilegios que tenía el usuario original.
16. Vaya a la parte 2.

##Parte 2: Parada del trabajo de Análisis de transmisiones
1.  Vaya a la extensión Análisis de transmisiones en el Portal de administración de Azure: ![graphic26][graphic26]
2.  Busque su trabajo y vaya a él: ![graphic27][graphic27]
3.  Vaya a la pestaña Entradas o Salidas en función de si está rotando las credenciales en una entrada o una salida. ![graphic28][graphic28]
4.  Haga clic en el comando Detener y confirme que el trabajo se ha detenido: ![graphic29][graphic29] Espere a que el trabajo se detenga.
5.  Busque la entrada/salida en la que desea girar las credenciales y vaya a ella: ![graphic30][graphic30]
6.  Vaya a la parte 3.

##Parte 3: Edición de las credenciales en el trabajo de Análisis de transmisiones

###Almacenamiento de blobs/almacenamiento de tablas
1.	Busque el Clave de cuenta de almacenamiento y pegue la clave recién generada: ![graphic31][graphic31]
2.	Haga clic en el comando Guardar y confirme que desea guardar los cambios: ![graphic32][graphic32]
3.	Al guardar los cambios se iniciará automáticamente una prueba de conexión ; asegúrese de que haya pasado correctamente.
4.	Vaya a la parte 4.

###Centros de eventos
1.	Busque el campo de clave de directiva del centro de eventos y pegue la clave recién generada: ![graphic33][graphic33]
2.	Haga clic en el comando Guardar y confirme que desea guardar los cambios: ![graphic34][graphic34]
3.	Una prueba de conexión se iniciará automáticamente al guardar los cambios; asegúrese de que haya pasado correctamente.
4.	Vaya a la parte 4.

###Power BI
1.	Haga clic en la autorización de renovación:  
* ![graphic35][graphic35]
* Obtendrá la siguiente confirmación:  
* ![graphic36][graphic36]
2.	Haga clic en el comando Guardar y confirme que desea guardar los cambios: ![graphic37][graphic37]
3.	Al guardar los cambios se iniciará automáticamente una prueba de conexión ; asegúrese de que haya pasado correctamente.
4.	Vaya a la parte 4.

###Base de datos SQL
1.	Busque los campos Nombre de usuario y Contraseña y pegue en ellos el conjunto de credenciales recién creado: ![graphic38][graphic38]
2.	Haga clic en el comando Guardar y confirme que desea guardar los cambios: ![graphic39][graphic39]
3.	Una prueba de conexión se iniciará automáticamente al guardar los cambios; asegúrese de que haya pasado correctamente.  
4.	Vaya a la parte 4.

##Parte 4: Inicio del trabajo desde la última vez que se detuvo
1.	Salga de la entrada/salida: ![graphic40][graphic40]
2.	Haga clic en el comando Iniciar: ![graphic41][graphic41]
3.	Elija la última vez que se detuvo y haga clic en Aceptar: ![graphic42][graphic42]
4.	Vaya a la parte 5.  

##Parte 5: Eliminación del antiguo conjunto de credenciales
Este elemento es aplicable a las siguientes entradas/salidas: * Almacenamiento de blobs * Centros de eventos * Base de datos SQL * Almacenamiento de tablas

###Almacenamiento de blobs/almacenamiento de tablas
Repita la Parte 1 para la clave de acceso usada previamente por su trabajo para renovar la clave de acceso ahora no utilizada.

###Centros de eventos
Repita la Parte 1 para la clave usada previamente por su trabajo para renovar la clave ahora no utilizada.

###Base de datos SQL
1.	Vaya a la ventana de consulta de la Parte 1 Paso 7 y escriba la consulta siguiente, reemplazando <previous_login_name> por el nombre de usuario usado previamente por su trabajo: `DROP LOGIN <previous_login_name>`  
2.	Haga clic en Ejecutar: ![graphic43][graphic43]  

Obtendrá la siguiente confirmación:

	Command(s) completed successfully.

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 

<!---HONumber=AcomDC_0204_2016-->