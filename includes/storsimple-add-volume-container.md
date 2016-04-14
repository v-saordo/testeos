<!--author=SharS last changed: 1/7/2016-->

#### Para agregar un contenedor de volúmenes

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Haga clic en **Agregar** en la parte inferior de la página. En el cuadro de diálogo **Crear contenedor de volúmenes**, realice lo siguiente:

  1. Proporcione un **Nombre** único para el contenedor de volúmenes. Este nombre puede contener 32 caracteres como máximo.
  2. Seleccione una **Cuenta de almacenamiento** para asociarla a este contenedor de volúmenes. Puede elegir entre una cuenta de almacenamiento existente dentro de la misma suscripción o seleccionar **Agregar más** para seleccionar una cuenta de almacenamiento de otra suscripción. También puede elegir la cuenta de almacenamiento que se generó primero cuando se creó el servicio.
  3. Especifique el ancho de banda como **Ilimitado** si desea consumir todo el ancho de banda disponible, o **Personalizado** para emplear controles de ancho de banda. Si elige el ancho de banda personalizado, proporcione un valor entre 1 y 1000 Mbps. Para asignar el ancho de banda en función de una programación, puede **Seleccionar una plantilla de ancho de banda**.
  4. Se recomienda que mantenga seleccionada la opción **Habilitar cifrado de almacenamiento en la nube** para cifrar los datos que se van a la nube. Deshabilite el cifrado solo si emplea otros métodos para cifrar los datos. No se puede modificar la configuración de cifrado una vez creado el contenedor de volúmenes.
  5. Proporcione una **Clave de cifrado de almacenamiento en la nube** que contenga entre 8 y 32 caracteres. El dispositivo usa esta clave para tener acceso a los datos cifrados. En el campo **Confirmar clave de cifrado de almacenamiento en la nube**, escriba de nuevo la clave de cifrado de almacenamiento en la nube para confirmarla. 
  6. Haga clic en la flecha para ir a la página siguiente.

    ![Creación de un contenedor de volúmenes con la plantilla de ancho de banda 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png)

3. Si especificó **Seleccionar una plantilla de ancho de banda**, elija en la lista desplegable de plantillas de ancho de banda existentes. Revise la configuración de programación y haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).

    ![Creación de un contenedor de volúmenes con la plantilla de ancho de banda 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png)

El contenedor de volúmenes se guardará y el contenedor de volúmenes recién creado se mostrará en la página **Contenedor de volúmenes**.
 

<!---HONumber=AcomDC_0114_2016-->