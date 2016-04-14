#### Para restaurar el dispositivo físico en el dispositivo virtual de StorSimple

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.

2. Abra la página **Dispositivo** y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

3. Seleccione un contenedor de volúmenes que le gustaría conmutar por error en el dispositivo virtual. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes dentro del contenedor. Seleccione un volumen y haga clic en **Desconectado** para desconectar el volumen. Repita este proceso para todos los volúmenes del contenedor de volúmenes.

4. Repita el paso anterior para todos los contenedores de volúmenes que desee conmutar por error en el dispositivo virtual.

5. En la página **Dispositivo**, seleccione el dispositivo que necesita conmutar por error y, a continuación, haga clic en **Conmutación por error** para abrir el asistente **Dispositivo de conmutación por error**.

6. En **Elegir el contenedor de volúmenes para la conmutación por error**, seleccione los contenedores de volumen que le gustaría conmutar por error. Para aparecer en esta lista, el contenedor de volúmenes debe contener una instantánea en la nube y desconectado. Si un contenedor de volúmenes que esperaba ver no está presente, cancele al asistente y compruebe que está desconectado.

7. En la siguiente página, en **Elegir un dispositivo de destino para los volúmenes** en los contenedores seleccionados, seleccione el dispositivo virtual en la lista desplegable de dispositivos disponibles. Solo los dispositivos que tienen la capacidad disponible se muestran en la lista.

8. Revise la configuración de conmutación por error en la página **Confirmar conmutación por error**. Si son correctos, haga clic en el icono de verificación.

Comenzará el proceso de conmutación por error. Cuando finalice la conmutación por error, vaya a la página de dispositivos y seleccione el dispositivo virtual que se utiliza como destino para el proceso de conmutación por error. Vaya a la página Contenedores de volúmenes. Deben aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

<!---HONumber=AcomDC_1217_2015-->