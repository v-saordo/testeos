<properties
	pageTitle="Referencia de la configuración de movilidad de Windows 10 | Microsoft Azure"
	description="Una lista completa de todas las opciones que se movilizan o de las que se realiza una copia de seguridad en Windows 10."
	services="active-directory"
    keywords="enterprise state roaming, nube de windows"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Referencia de la configuración de movilidad de Windows 10

Lo siguiente es una lista completa de todas las opciones que se movilizan o de las que se realiza una copia de seguridad en Windows 10.

##Interfaces y puntos de conexión
Consulte la tabla siguiente para ver un resumen de los dispositivos y tipos de cuenta compatibles con el marco de sincronización, copia de seguridad y restauración en Windows 10.

| Tipo de cuenta y operación | Escritorio | Móvil |
|----------------------------|---------|--------|
|Azure Active Directory: sincronización| Sí | No |
|Azure Active Directory: copia de seguridad/restauración| No | No |
|Cuenta de Microsoft: sincronización|Sí | Sí |
|Cuenta de Microsoft: copia de seguridad/restauración| No | Sí |


##¿Qué es una copia de seguridad?
La configuración de Windows se sincroniza normalmente de forma predeterminada pero de algunas configuraciones solo se realiza una copia de seguridad, como la lista de aplicaciones instaladas en un dispositivo. Si un usuario deshabilita la sincronización en el dispositivo mediante la aplicación Configuración, los datos de la aplicación que se suelen sincronizar se convierten en datos de copia de seguridad solamente. Solo se puede tener acceso a los datos de copia de seguridad mediante la operación de restauración durante la primera experiencia de ejecución de un nuevo dispositivo. Las copias de seguridad se pueden deshabilitar mediante la configuración del dispositivo, y se pueden administrar y eliminar mediante la cuenta OneDrive del usuario.

## Introducción a la configuración de Windows
Los grupos de configuración siguientes están disponibles para que los usuarios finales puedan habilitar o deshabilitar en ellos la sincronización de configuración en dispositivos de Windows 10.

- Tema: fondo de escritorio, icono de usuario, posición de la barra de tareas, etc. 
- Configuración de Internet Explorer: historial de exploración, direcciones URL escritas, favoritos, etc. 
- Contraseñas: [Caja de seguridad de credenciales de Windows](https://technet.microsoft.com/library/jj554668.aspx), incluidos perfiles de Wi-Fi 
- Preferencias de idioma: diccionario de ortografía, configuración de idioma del sistema 
- Facilidad de acceso: narrador, teclado en pantalla, lupa 
- Otras configuraciones de Windows: consultar Detalles de configuración de Windows

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)
 
## Detalles de configuración de Windows
En la tabla siguiente, las entradas Otros en la columna Grupo de la configuración se refieren a configuraciones que se pueden deshabilitar en Configuración > Cuentas > Sincronizar la configuración > Otras configuraciones de Windows.

Las entradas Interno de la columna Grupo de la configuración hacen referencia a la configuración y las aplicaciones que solo se pueden deshabilitar de la sincronización dentro de la propia aplicación o al deshabilitar la sincronización para todo el dispositivo mediante la administración de dispositivos móviles (MDM) o la configuración de directivas de grupo. Las configuraciones que no se movilizan o sincronizan no pertenecerán a ningún grupo.


| Settings | Escritorio | Móvil | Grupo |
|----------------------------------|---------|---------|-------|
| **Cuentas**: imagen de la cuenta | sync |X |Tema |
| **Cuentas**: otras configuraciones de la cuenta |X |X | |
| **Ancho de banda móvil avanzado**: conexión a Internet que comparte el nombre de red (permite la detección automática de zonas con cobertura inalámbrica móvil a través de Bluetooth)|sync |sync |Contraseñas |
|**Datos de la aplicación**: las aplicaciones individuales pueden sincronizar datos|sincronización copia de seguridad | sincronización copia de seguridad|interno |
|**Lista de aplicaciones**: lista de aplicaciones instaladas |X |backup |Otros |
|**Bluetooth**: toda la configuración de Bluetooth |X |X | |
|**Símbolo del sistema**: toda la configuración de línea de comandos |sync| |X |Otros
|**Cortana**: activar o desactivar |X |X | |
|**Cortana**: habilitar Cortana en la pantalla de bloqueo |X |X | |
|**Cortana**: nombre de usuario |sync |sync |interno|
|**Cortana**: leer SMS en voz alta |X |sync |interno|
|**Cortana**: Búsqueda segura |X |sync |interno|
|**Cortana**: buscar información sobre vuelos, etc.|X |sync |interno|
|**Credenciales**: Caja de seguridad de credenciales |sync |sync |contraseña|
|**Fecha, hora y región**: hora automática (sincronización de tiempo de Internet) |sync |sync |language|
|**Fecha, hora y región**: formato de 24 horas|sync |sync |language|
|**Fecha, hora y región**: fecha y hora|sync |X |language|
|**Fecha, hora y región**: zona horaria | |X |language|
|**Fecha, hora y región**: horario de verano|sync |X |language|
|**Fecha, hora y región**: país o región |sync |X |language|
|**Fecha, hora y región**: primer día de la semana |sync |X |language|
|**Fecha, hora y región**: formato de región (configuración regional) |sync |X |language|
|**Fecha, hora y región**: fecha corta |sync |X |language|
|**Fecha, hora y región**: fecha larga |sync |X |language|
|**Fecha, hora y región**: hora corta |sync |X |language|
|**Fecha, hora y región**: hora larga |sync |X |language|
|**Personalización del escritorio**: tema de escritorio (fondo, color del sistema, sonidos del sistema predeterminados, protector de pantalla) |sync |X |Tema|
|**Personalización del escritorio**: papel tapiz de presentación |sync |X |Tema|
|**Personalización del escritorio**: configuración de la barra de tareas (posición, ocultar automáticamente, etc.). |sync |X |Tema|
|**Personalización del escritorio**: diseño de pantalla de inicio |sync |backup ||
|**Dispositivos**: impresoras compartidas a las que se conecta |sync | X |otros |
|**Explorador Edge**: lista de lectura |sync |sync |interno|
|**Explorador Edge**: favoritos |sync |sync |interno|
|**Explorador Edge**: el resto de la configuración de Edge|X |X ||
|**Contraste alto**: activar o desactivar |sync |sync |Facilidad de acceso|
|**Contraste alto**: configuración de temas|sync |X ||Facilidad de acceso|
|**Internet Explorer**: abrir pestañas (dirección URL y título)|sync |sync |Internet Explorer|
|**Internet Explorer**: lista de lectura|sync |sync |Internet Explorer|
|**Internet Explorer**: direcciones URL escritas|sync |sync |Internet Explorer|
|**Internet Explorer**: historial de exploración|sync |sync |Internet Explorer|
|**Internet Explorer**: favoritos|sync |sync |Internet Explorer|
|**Internet Explorer**: direcciones URL excluidas|sync |sync |Internet Explorer|
|**Internet Explorer**: páginas de inicio|sync |sync |Internet Explorer|
|**Internet Explorer**: sugerencias de dominio|sync |sync |Internet Explorer|
|**Teclado**: los usuarios pueden activar o desactivar el teclado en pantalla|sync |X |Facilidad de acceso|
|**Teclado**: activar sí temporal (desactivado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Teclado**: activar teclas de filtro (desactivado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Teclado**: activar teclas de alternancia (desactivado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Internet Explorer**: idioma de dominio: QWERTY chino simplificado - habilitar autoaprendizaje|sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - habilitar clasificación dinámica del candidato|sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - juego de caracteres de chino simplificado|sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - juego de caracteres de chino tradicional|sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - pinyin aproximado|sync |sync |Idioma|
|**Idioma**: QWERTY chino simplificado - pares aproximados|sync |sync |Idioma|
|**Idioma**: QWERTY chino simplificado - pinyin completo||sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - pinyin doble|sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - corrección automática de lectura|sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - tecla de modificador C/E, MAYÚS|sync |X |Idioma|
|**Idioma**: QWERTY chino simplificado - tecla de modificador C/E, Ctrl|sync |X |Idioma|
|**Idioma**: chino simplificado WUBI - modo de entrada de carácter único |sync |X |Idioma|
|**Idioma**: WUBI chino simplificado - mostrar la codificación restante del candidato |sync |X |Idioma|
|**Idioma**: WUBI chino simplificado - pitido cuando la codificación 4 no sea válida|sync |X |Idioma|
|**Idioma**: Bopomofo chino simplificado - incluir CJK Ext-A|sync |X |Idioma|
|**Idioma**: IME japonés - escritura predictiva y palabras personalizadas|sync |sync |Idioma|
|**Idioma**: IME coreano (KOR)|X |X |Idioma|
|**Idioma**: reconocimiento de escritura a mano|X |X |Idioma|
|**Idioma**: perfil de lenguaje|sync |backup |Idioma|
|**Idioma**: corrector ortográfico - autocorrección y resaltar errores ortográficos|sync |backup |Idioma|
|**Idioma**: lista de teclados|sync |backup |Idioma|
|**Pantalla de bloqueo**: toda la configuración de pantalla de bloqueo|X |X ||
|**Lupa**: activar o desactivar (alternancia de maestro)|X |backup |Facilidad de acceso|
|**Lupa**: activar o desactivar inversión del color (desactivado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Lupa**: seguimiento - seguimiento del foco del teclado|sync |X |Facilidad de acceso|
|**Lupa**: seguimiento - seguimiento del cursor del mouse|sync |X |Facilidad de acceso|
|**Lupa**: iniciar cuando los usuarios inicien sesión (desactivado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Mouse**: cambiar el tamaño del cursor del mouse|sync |X |otros|
|**Mouse**: cambiar el color del cursor del mouse|sync |X |otros|
|**Mouse**: todas las demás configuraciones|X |X ||
|**Narrador**: inicio rápido|sync |X |Facilidad de acceso|
|**Narrador**: los usuarios pueden cambiar el tono de voz del narrador|sync |X |Facilidad de acceso|
|**Narrador**: los usuarios pueden activar o desactivar las sugerencias de lectura del narrador para los elementos comunes (activado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Narrador**: los usuarios pueden activar o desactivar si pueden oír los caracteres escritos (activado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Narrador**: los usuarios pueden activar o desactivar si pueden oír las palabras escritas (activado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Narrador**: insertar cursor en función del narrador (activado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Narrador**: habilitar resaltado visual del cursor del narrador (activado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Narrador**: reproducir entradas de sonido (activado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Narrador**: activar teclas del teclado táctil al levantar el dedo (desactivado de forma predeterminada)|sync |sync |Facilidad de acceso|
|**Facilidad de acceso**: establecer el grosor del cursor intermitente|sync |X |Facilidad de acceso|
|**Facilidad de acceso**: quitar imágenes de fondo (desactivado de forma predeterminada)|sync |X |Facilidad de acceso|
|**Encendido y suspensión**: todas las configuraciones|X |X ||
|**Iniciar personalización de la pantalla**: color del sistema|sync |sync |Tema|
|**Escritura**: diccionario ortográfico|sync |backup |Idioma|
|**Escritura**: autocorrección de palabra escrita incorrectamente|sync |backup |Idioma|
|**Escritura**: resaltar palabras incorrectas|sync |backup |Idioma|
|**Escritura**: mostrar sugerencias de texto al escribir|sync |backup |Idioma|
|**Escritura**: agregar un espacio después de seleccionar una sugerencia de texto|sync |backup |Idioma|
|**Escritura**: agregar un punto al pulsar dos veces la barra espaciadora|sync |backup |Idioma|
|**Escritura**: en mayúsculas la primera letra de cada frase|sync |backup |Idioma|
|**Escritura**: escribir todo en mayúsculas al pulsar dos veces la tecla MAYÚS|sync |backup |Idioma|
|**Escritura**: reproducir sonidos de teclas al escribir|sync |backup |Idioma|
|**Escritura**: datos de personalización para teclado táctil|sync |backup |Idioma|
|**Wi-Fi**: perfiles Wi-Fi (solo WPA)|sync |sync |Contraseñas|


## Temas relacionados
- [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitación de Enterprise State Roaming en Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Preguntas más frecuentes sobre itinerancia de datos y configuración](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Configuración de MDM y directivas de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)






  

<!---HONumber=AcomDC_0204_2016-->