<properties 
   pageTitle="Configurar CHAP para su dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo configurar el protocolo de autenticación por desafío mutuo (CHAP) en un dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="02/12/2016"
   ms.author="alkohli" />

# Configurar CHAP para el dispositivo StorSimple

Este tutorial explica cómo configurar CHAP para su dispositivo StorSimple. El procedimiento descrito en este artículo se aplica a la serie 8000 de StorSimple, así como a dispositivos StorSimple 1200.

CHAP significa Protocolo de autenticación por desafío mutuo. Es un esquema de autenticación utilizado por los servidores para validar la identidad de los clientes remotos. La verificación se basa en una contraseña o clave compartidas. CHAP puede ser unidireccional o mutuo (bidireccional). CHAP unidireccional es cuando el destino autentica el iniciador. CHAP mutuo o invertido, por otra parte, requiere que el destino autentique el iniciador y luego que el iniciador autentique el destino. La autenticación del iniciador puede implementarse sin la autenticación del destino. Sin embargo, la autenticación del destino solo puede implementarse si también se implementa la autenticación del iniciador.

Como procedimiento recomendado, aconsejamos utilizar CHAP para mejorar la seguridad iSCSI.

>[AZURE.NOTE] Tenga en cuenta que los dispositivos StorSimple no admiten IPSEC actualmente.

Los parámetros de CHAP del dispositivo StorSimple pueden configurarse de las siguientes formas:

- Autenticación unidireccional

- Autenticación bidireccional o mutua o inversa

En cada uno de estos casos, deben configurarse el portal del dispositivo y el software del iniciador iSCSI del servidor. En el siguiente tutorial se describen los pasos detallados para esta configuración.

## Autenticación unidireccional

En la autenticación unidireccional, el destino autentica el iniciador. Esta autenticación requiere que configure los parámetros del iniciador de CHAP en el dispositivo StorSimple y el software del iniciador iSCSI en el host. Los procedimientos detallados para su dispositivo StorSimple y el host de Windows se describen a continuación.

#### Para configurar su dispositivo para la autenticación unidireccional

1. En el Portal de Azure clásico, en la página **Dispositivos**, haga clic en la pestaña **Configurar**.

    ![Iniciador CHAP](./media/storsimple-configure-chap/IC740943.png)

2. Desplácese hacia abajo en esta página y, en la sección **Iniciador de CHAP**:
													
	1. Proporcione un nombre de usuario para su iniciador de CHAP.

	2. Proporcionar una contraseña para su iniciador de CHAP.

   		 > [AZURE.IMPORTANT] El nombre de usuario de CHAP no puede contener más de 233 caracteres. La contraseña de CHAP debe contener entre 12 y 16 caracteres. Los nombres de usuario o contraseñas de mayor longitud generarán un error de autenticación en el host de Windows.
    
	3. Confirme la contraseña.

4. Haga clic en **Guardar**. Aparecerá un mensaje de confirmación. Haga clic en **Aceptar** para guardar los cambios.
#### Para configurar la autenticación unidireccional en el servidor host de Windows

1. En el servidor host de Windows, inicie el iniciador iSCSI.

2. En la ventana **Propiedades del iniciador iSCSI**, realice los siguientes pasos:
													
	1. Haga clic en la pestaña **Detección**.

		![Propiedades del iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)

	2. Haga clic en **Detectar portal**.

3. En el cuadro de diálogo **Detectar portal de destino**:
													
	1. Especifique la dirección IP de su dispositivo.

	3. Haga clic en **Avanzadas**.

		![Detectar portal de destino](./media/storsimple-configure-chap/IC740945.png)

4. En el cuadro de diálogo **Configuración avanzada**:
													
	1. Active la casilla **Habilitar inicio de sesión CHAP**.

	2. En el campo **Nombre**, proporcione el nombre de usuario que especificó para el iniciador de CHAP en el Portal clásico.

	3. En el campo **Secreto de destino**, proporcione la contraseña que especificó para el iniciador de CHAP en el Portal clásico.

	4. Haga clic en **Aceptar**.

		![Generalidades de configuración avanzada](./media/storsimple-configure-chap/IC740946.png)

5. En la pestaña **Destinos** de la ventana **Propiedades del iniciador iSCSI**, el estado del dispositivo debe aparecer como **Conectado**. Si usa un dispositivo StorSimple 1200, cada volumen se montará como un destino iSCSI como se muestra a continuación. Por lo tanto, los pasos 3 y 4 deberán repetirse para cada volumen.

	![Volúmenes montados como destinos independientes](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Si cambia el nombre de iSCSI, el nuevo nombre se utilizará para nuevas sesiones de iSCSI. La nueva configuración no se utiliza para las sesiones existentes hasta que cierra sesión y vuelve a iniciar sesión.

Para obtener más información acerca de la configuración de CHAP en el servidor de host de Windows, vaya a [Consideraciones adicionales](#additional-considerations).


## Autenticación bidireccional o mutua

En la autenticación bidireccional, el destino autentica el iniciador y luego el iniciador autentica el destino. Esto requiere que el usuario configure los parámetros del iniciador de CHAP, así como los parámetros de CHAP inverso en el dispositivo y el software del iniciador iSCSI en el host. Los siguientes procedimientos describen los pasos para configurar la autenticación mutua en el dispositivo y en el host de Windows.

#### Para configurar su dispositivo para la autenticación mutua

1. En el Portal de Azure clásico, en la página **Dispositivos**, haga clic en la pestaña **Configurar**.

    ![Destino CHAP](./media/storsimple-configure-chap/IC740948.png)

2. Desplácese hacia abajo en esta página y, en la sección **Destino de CHAP**:
													
	1. Proporcione un**Nombre de usuario de CHAP inverso** para su dispositivo.

	2. Proporcione una **Contraseña de CHAP inverso**para su dispositivo.

	3. Confirme la contraseña.

3. En la sección **Iniciador de CHAP**:
												
	1. Proporcione un **nombre de usuario** para su dispositivo.

	1. Proporcione una **contraseña** para su dispositivo.

	3. Confirme la contraseña.

4. Haga clic en **Guardar**. Aparecerá un mensaje de confirmación. Haga clic en **Aceptar** para guardar los cambios.

#### Para configurar la autenticación bidireccional en el servidor host de Windows

1. En el servidor host de Windows, inicie el iniciador iSCSI.

2. En la ventana **Propiedades del iniciador iSCSI** haga clic en la pestaña **Configuración**.

3. Haga clic en **CHAP**.

4. En el cuadro de diálogo **Secreto CHAP mutuo del iniciador iSCSI**:
													
	1. Escriba la **Contraseña de CHAP inverso** que configuró en el Portal de Azure clásico.

	2. Haga clic en **Aceptar**.

		![Secreto CHAP mutuo del iniciador iSCSI](./media/storsimple-configure-chap/IC740949.png)

5. Haga clic en la pestaña **Destinos**.

6. Haga clic en el botón **Conectar**.

7. En el cuadro de diálogo **Conectar a destino**, haga clic en **Avanzado**.

8. En el cuadro de diálogo **Propiedades avanzadas**:
													
	1. Active la casilla **Habilitar inicio de sesión CHAP**.

	2. En el campo **Nombre**, proporcione el nombre de usuario que especificó para el iniciador de CHAP en el Portal clásico.

	3. En el campo **Secreto de destino**, proporcione la contraseña que especificó para el iniciador de CHAP en el Portal clásico.

	4. Active la casilla **Realizar autenticación mutua**.

		![Autenticación mutua de configuración avanzada](./media/storsimple-configure-chap/IC740950.png)

	5. Haga clic en **Aceptar** para completar la configuración de CHAP.
	 
Para obtener más información acerca de la configuración de CHAP en el servidor de host de Windows, vaya a [Consideraciones adicionales](#additional-considerations).

## Consideraciones adicionales

La característica **Conexión rápida** no admite las conexiones con CHAP habilitado. Cuando CHAP esté habilitado, asegúrese de utilizar el botón **Conectar** que está disponible en la pestaña **Destinos** para conectarse a un destino.

![Conectar a destino](./media/storsimple-configure-chap/IC740947.png)

En el cuadro de diálogo **Conectar a destino** que aparece, active la casilla **Agregar esta conexión a la lista de destinos favoritos**. Esto garantiza que cada vez que se reinicie el equipo, se intente restaurar la conexión a los destinos favoritos de iSCSI.

## Errores durante la configuración

Si su configuración de CHAP es incorrecta, es probable que vea el mensaje de error **Falla de autenticación**.

## Verificación de la configuración de CHAP

Puede verificar que CHAP esté en uso mediante los siguientes pasos.

#### Para verificar su configuración de CHAP

1. Haga clic en **Destinos favoritos**.

2. Seleccione el destino para el que se ha habilitado la autenticación.

3. Haga clic en **Detalles**.

    ![Destinos favoritos de las propiedades del iniciador iSCSI](./media/storsimple-configure-chap/IC740951.png)

4. En el cuadro de diálogo **Detalles de destino favorito**, observe la entrada del campo **Autenticación**. Si la configuración se realizó correctamente, debe decir **CHAP**.

    ![Detalles de destino favorito](./media/storsimple-configure-chap/IC740952.png)

## Pasos siguientes

- Obtenga más información acerca de la [Seguridad de StorSimple](storsimple-security.md).

- Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0218_2016-->