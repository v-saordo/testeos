<properties 
   pageTitle="Pruebas de configuración del Administrador de tráfico | Microsoft Azure"
   description="Este artículo le ayudará a probar la configuración del Administrador de tráfico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="joaoma" />

# Pruebe la configuración del Administrador de tráfico

La mejor forma de probar la configuración del Administrador de tráfico es configurar varios clientes y luego desactivar los extremos del perfil, que constan de servicios en la nube y sitios web, de uno en uno. Las siguientes sugerencias le ayudarán a probar el perfil del Administrador de tráfico.

## Pasos de pruebas básicos

- **Establezca el TTL de DNS muy bajo** de forma que los cambios se propaguen rápidamente; por ejemplo, en 30 segundos.
- **Conozca las direcciones IP de los servicios en la nube de Azure y los sitios web** del perfil que prueba.
- **Use herramientas que permiten resolver un nombre de DNS en una dirección IP** y mostrar dicha dirección. Compruebe que el nombre de dominio de la empresa se resuelve en las direcciones IP de los extremos del perfil. Deben resolverse de manera coherente con el método de enrutamiento del tráfico del perfil del Administrador de tráfico. Si se encuentra en un equipo que ejecuta Windows, puede usar la herramienta Nslookup.exe desde un símbolo del sistema o de Windows PowerShell. También dispone en Internet de otras herramientas disponibles públicamente que le permiten "profundizar" en la dirección IP.

### Para comprobar el perfil del Administrador de tráfico con nslookup

1. Abra un símbolo del sistema o de Windows PowerShell como administrador.
2. Escriba `ipconfig /flushdns` para vaciar la memoria caché de resolución DNS.
3. Escriba `nslookup <your Traffic Manager domain name>`. Por ejemplo, el siguiente comando comprueba el dominio con el prefijo *myapp.contoso* nslookup myapp.contoso.trafficmanager.net. A continuación se muestra un resultado típico:
   - El nombre de DNS y la dirección IP del servidor DNS al que se accede para resolver este nombre de dominio del Administrador de tráfico.
   - El nombre de dominio del Administrador de tráfico que especificó en la línea de comandos después de "nslookup" y la dirección IP en la que se resuelve el dominio del Administrador de tráfico. La segunda dirección IP es la que es importante comprobar. Debe coincidir con una dirección IP virtual (VIP) pública de uno de los servicios en la nube o los sitios web del perfil del Administrador de tráfico que prueba.

## Prueba de los métodos de enrutamiento del tráfico

### Para probar un método de enrutamiento del tráfico de conmutación por error

1. Deje todos los extremos activados.
2. Use un único cliente.
3. Solicite la resolución DNS del nombre de dominio de la empresa con la herramienta Nslookup.exe o una utilidad similar.
4. Asegúrese de que la dirección IP resuelta que obtiene sea para el extremo principal
5. Desactive el extremo principal o elimine el archivo de supervisión para que el Administrador de tráfico considere que está desactivado.
6. Espere durante el período de vida (TTL) de DNS del Administrador de tráfico, más dos minutos adicionales. Por ejemplo, si el TTL de DNS es de 300 segundos (cinco minutos), debe esperar siete minutos.
7. Vacíe la memoria caché del cliente DNS y solicite la resolución DNS. En Windows, puede vaciar la memoria caché de DNS emitiendo el comando ipconfig /flushdns en el símbolo del sistema o de Windows PowerShell.
8. Asegúrese de que la dirección IP que obtiene sea para el extremo secundario.
9. Repita el proceso desactivando el extremo secundario y luego el terciario, y así sucesivamente. Cada vez, asegúrese de que la resolución DNS devuelve la dirección IP del siguiente extremo de la lista. Cuando haya desactivado todos los extremos, debería volver a obtener la dirección IP del extremo principal.

### Para probar un método de enrutamiento del tráfico round robin

1. Deje todos los extremos activados.
2. Use un único cliente.
3. Solicite la resolución DNS del dominio de la empresa con la herramienta Nslookup.exe o una utilidad similar.
4. Asegúrese de que la dirección IP que obtiene sea una de las que aparecen en la lista.
5. Vacíe la memoria caché del cliente DNS y repita los pasos 3 y 4 una y otra vez. Debería ver diferentes direcciones IP devueltas para cada uno de los extremos. Seguidamente, se repetirá el proceso.

### Para probar un método de enrutamiento del tráfico de rendimiento

Para probar eficazmente un método de enrutamiento del tráfico de rendimiento, deberá tener clientes situados en distintas partes del mundo. Puede crear clientes en Azure que intenten llamar a los servicios a través del nombre de dominio de la empresa. Si la organización es global, también puede iniciar sesión de forma remota en clientes en otras partes del mundo y realizar pruebas desde dichos clientes.

Hay servicios gratuitos de indagación y de búsqueda DNS basada en web disponibles. Algunos de estos proporcionan la capacidad de comprobar la resolución de nombres DNS desde varias ubicaciones. Realice una búsqueda en "Búsqueda DNS" para obtener ejemplos. Otra opción es usar una solución de terceros como, por ejemplo, Gomez o Keynote, para confirmar que los perfiles distribuyen el tráfico según lo esperado.

## Pasos siguientes

[Consideraciones de rendimiento sobre el Administrador de tráfico](traffic-manager-performance-considerations.md)

[Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)




 

<!---HONumber=AcomDC_1210_2015-->