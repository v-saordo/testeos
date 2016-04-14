<!--author=v-sharos last changed: 11/06/15-->

#### Para agregar una directiva de copia de seguridad de StorSimple

1. En la página **Inicio rápido** del dispositivo, haga clic en la pestaña **Directivas de copia de seguridad**. Esto le llevará a la página **Directivas de copia de seguridad**.

2. En la parte inferior de la página, haga clic en **Agregar** para iniciar el asistente para Agregar directiva de copia de seguridad.

    ![Incorporación de una directiva de copia de seguridad 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)

3. En el cuadro de diálogo **Agregar directiva de copia de seguridad**, en **Definir directiva de copia de seguridad**, haga lo siguiente:

    1. Especifique un nombre que tenga entre 3 y 150 caracteres para la directiva de copia de seguridad.
    2. Haga clic en las casillas para asignar uno o más volúmenes a esta directiva de copia de seguridad. Tenga en cuenta que no puede seleccionar volúmenes que usen diferentes proveedores de servicios en la nube. Si utiliza varios proveedores de servicios en la nube, en función de su primera selección, en la lista desplegable se mostrará los volúmenes que pertenecen únicamente a ese proveedor de servicios en la nube. Esto le permitirá agrupar los volúmenes que pertenecen a un único proveedor de servicios en la nube para tomar una instantánea.
    3. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) para ir a la página siguiente.

     ![Incorporación de una directiva de copia de seguridad 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)

4. En **Definir una programación**, haga lo siguiente:
    1. En el cuadro **Tipo de copia de seguridad**, seleccione **Instantánea en la nube** o **Instantánea local** en la lista desplegable.
    2. Indique la frecuencia de las copias de seguridad (especifique un número y luego elija **Días** o **Semanas** en la lista desplegable.
    3. Especifique una programación de retención.
    4. Escriba una fecha y hora para que comience la directiva de copia de seguridad.  
    6. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) para guardar la directiva.

La directiva recién agregada se mostrará en la vista tabular en la página **Directivas de copia de seguridad**.
 

<!---HONumber=AcomDC_1217_2015-->