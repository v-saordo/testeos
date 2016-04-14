<!--v-sharos 10/13/2105 virtual device security-->

Tenga en cuenta las siguientes consideraciones de seguridad al utilizar el dispositivo virtual de StorSimple:

- El dispositivo virtual se protege mediante su suscripción de Microsoft Azure. Esto significa que si está utilizando el dispositivo virtual y se pone en peligro su suscripción de Azure, los datos almacenados en el dispositivo virtual también se ponen en peligro.

- La clave pública del certificado usada para cifrar los datos almacenados en Azure StorSimple está disponible de forma segura en el Portal de Azure clásico; la clave privada se conserva con el dispositivo StorSimple. En el dispositivo virtual de StorSimple, las claves públicas y privadas se almacenan en Azure.

- El dispositivo virtual está hospedado en el centro de datos de Microsoft Azure.

<!---HONumber=AcomDC_0128_2016-->