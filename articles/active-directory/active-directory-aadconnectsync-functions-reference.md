<properties
	pageTitle="Azure AD Connect Sync: referencia de funciones | Microsoft Azure"
	description="Referencia de expresiones declarativas de aprovisionamiento en la sincronización de Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="markusvi"/>


# Azure AD Connect Sync: referencia de funciones


En Sincronización de Azure Active Directory, las funciones se usan para manipular un valor de atributo durante la sincronización. <br> La sintaxis de las funciones se expresa con el siguiente formato: <br> `<output type> FunctionName(<input type> <position name>, ..)`

Si una función está sobrecargada y acepta la sintaxis múltiple, se enumeran todas las sintaxis válidas.<br> Las funciones están fuertemente tipadas y comprueban que el tipo pasado coincide con el tipo documentado.<br> Se produce un error si el tipo no coincide.

Los tipos se expresan con la siguiente sintaxis:

- **bin**: binario
- **bool**: booleano
- **dt**: fecha y hora UTC
- **enum**: enumeración de constantes conocidas
- **exp**: expresión, que se espera que evalúe en un booleano
- **mvbin**: binario de varios valores
- **mvstr**: referencia de varios valores
- **num**: numérico
- **ref**: referencia de valor único
- **str**: cadena de valor único
- **var**: una variante de (casi) cualquier otro tipo
- **void**: no devuelve un valor



## Referencia de funciones

----------
**Conversión:**

[CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [CGuid](#cguid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromBase64](#convertfrombase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromUTF8Hex](#convertfromutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [CNum](#cnum) &nbsp;&nbsp;&nbsp;&nbsp; [CRef](#cref) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromGuid](#StringFromGuid) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromSid](#stringfromsid)

**Fecha y hora:**

[DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate)

**Directorio**

[DNComponent](#dncomponent) &nbsp;&nbsp;&nbsp;&nbsp; [DNComponentRev](#dncomponentrev) &nbsp;&nbsp;&nbsp;&nbsp; [EscapeDNComponent](#escapedncomponent)

**Evaluación**:

[IsBitSet](#isbitset) &nbsp;&nbsp;&nbsp;&nbsp; [IsDate](#isdate) &nbsp;&nbsp;&nbsp;&nbsp; [IsEmpty](#isempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsGuid](#isguid) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsNumeric](#isnumeric) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring)

**Matemático:**

[BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [BitOr](#bitor) &nbsp;&nbsp;&nbsp;&nbsp; [RandomNum](#randomnum)

**Con varios valores**

[Contains](#contains) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [ItemOrNull](#itemornull) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)

**Flujo del programa:**

[Error](#error) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)


**Texto**

[GUID](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [InStrRev](#instrrev) &nbsp;&nbsp;&nbsp;&nbsp; [LCase](#lcase) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Len](#len) &nbsp;&nbsp;&nbsp;&nbsp; [LTrim](#ltrim) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [PadLeft](#padleft) &nbsp;&nbsp;&nbsp;&nbsp; [PadRight](#padright) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [ReplaceChars](#replacechars) &nbsp;&nbsp;&nbsp;&nbsp; [Right](#right) &nbsp;&nbsp;&nbsp;&nbsp; [RTrim](rtrim) &nbsp;&nbsp;&nbsp;&nbsp; [Trim](#trim) &nbsp;&nbsp;&nbsp;&nbsp; [UCase](#ucase) &nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

----------
### BitAnd

**Descripción:**<br> la función BitAnd establece los bits especificados en un valor.

**Sintaxis:**<br> `num BitAnd(num value1, num value2)`

- value1, value2: valores numéricos que deberían estar unidos por el operador AND

**Comentarios:**<br> esta función convierte ambos parámetros en la representación binaria y establece un bit en:

- 0: si uno o ambos de los bits correspondientes en *máscara* y *marca* son 0
- 1: si ambos de los bits correspondientes son 1.

En otras palabras, devuelve 0 en todos los casos excepto cuando los bits correspondientes de los dos parámetros son 1.

**Ejemplo:**<br>`BitAnd(&HF, &HF7)`<br> devuelve 7 porque los valores hexadecimales "F" y "F7" evalúan este valor.

----------
### BitOr

**Descripción:** <br> la función BitAnd establece los bits especificados en un valor.

**Sintaxis:** <br> `num BitOr(num value1, num value2)`

- value1, value2: valores numéricos que deberían estar unidos por el operador OR

**Comentarios:**<br> esta función convierte ambos parámetros en la representación binaria y establece un bit en 1 si uno o ambos de los bits correspondientes en la máscara y marca son 1 y en 0 si los bits correspondientes son 0. <br> En otras palabras, devuelve 1 en todos los casos excepto cuando los bits correspondientes de ambos parámetros son 0.

----------
### CBool

**Descripción:**<br> la función CBool devuelve un valor de tipo booleano en función de la expresión evaluada

**Sintaxis:** <br> `bool CBool(exp Expression)`

**Comentarios:**<br> si la expresión se evalúa como un valor distinto de cero, CBool devuelve True. En caso contrario devuelve False.


**Ejemplo:**<br> `CBool([attrib1] = [attrib2])` <br>

Devuelve True si ambos atributos tienen el mismo valor.




----------
### CDate

**Descripción:**<br> la función CDate devuelve DateTime de UTC desde una cadena. DateTime no es un tipo de atributo nativo en Sincronización pero lo usan algunas funciones.

**Sintaxis:**<br> `dt CDate(str value)`

- Valor: una cadena con una fecha, hora y opcionalmente, una zona horaria

**Comentarios:**<br> la cadena devuelta siempre está en UTC.

**Ejemplo:**<br> `CDate([employeeStartTime])` <br> devuelve DateTime según la hora de inicio del empleado

`CDate("2013-01-10 4:00 PM -8")`<br> Devuelve DateTime que representa "2013-01-11 12:00 AM"




----------
### CGuid

**Descripción:**<br> la función CGuid convierte la representación de cadena de un GUID en la representación binaria.

**Sintaxis:**<br> `bin CGuid(str GUID)GUID`

- Una cadena con un formato que sigue este patrón: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}




----------
### Contains

**Descripción:**<br> la función Contains busca una cadena dentro de un atributo de varios valores

**Sintaxis:**<br> `num Contains (mvstring attribute, str search)` - distingue mayúsculas y minúsculas<br> `num Contains (mvstring attribute, str search, enum Casetype)`<br> `num Contains (mvref attribute, str search)` - distingue mayúsculas y minúsculas

- atributo: el atributo con varios valores de búsqueda.<br>
- búsqueda: cadena para buscar en el atributo.<br>
- Casetype: CaseInsensitive o CaseSensitive.<br>

Devuelve el índice en el atributo de varios valores donde se ha encontrado la cadena. Si no se encuentra la cadena, se devuelve 0.


**Comentarios:**<br> para los atributos de cadena de varios valores, la búsqueda encontrará subcadenas en los valores.<br> Para los atributos de referencia, la cadena de búsqueda debe coincidir exactamente con el valor para que se considere una coincidencia.

**Ejemplo:**<br>`IIF(Contains([proxyAddresses],”SMTP:”)>0,[proxyAddresses],Error(“No primary SMTP address found.”))`<br> si el atributo proxyAddresses tiene una dirección de correo electrónico principal (indicada por mayúsculas "SMTP:"), devuelve el atributo proxyAddress. De lo contrario devuelve un error.




----------
### ConvertFromBase64

**Descripción:**<br> la función ConvertFromBase64 convierte el valor codificado en base64 especificado en una cadena normal.

**Sintaxis:**<br>`str ConvertFromBase64(str source)` supone Unicode para la codificación <br>`str ConvertFromBase64(str source, enum Encoding)`

- origen: cadena codificada en Base64<br>
- Codificación: Unicode, ASCII, UTF8

**Ejemplo**<br> `ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`<br> `ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos ejemplos devuelven "*Hola a todos*"




----------
### ConvertFromUTF8Hex

**Descripción:**<br> la función ConvertFromUTF8Hex convierte el valor codificado hexadecimal UTF8 especificado en una cadena.

**Sintaxis:**<br> `str ConvertFromUTF8Hex(str source)`

- origen: cadena codificada de 2 bytes UTF8

**Comentarios:**<br> la diferencia entre esta función y ConvertFromBase64(,UTF8) en ese resultado es que el resultado es descriptivo para el atributo DN.<br> Azure Active Directory utiliza este formato como DN.

**Ejemplo:**<br> `ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`<br> devuelve "*Hola a todos*"




----------
### ConvertToBase64

**Descripción:**<br> la función ConvertToBase64 convierte una cadena en una cadena base64 de Unicode.<br> Convierte el valor de una matriz de enteros en su representación de cadena equivalente que se codifica con dígitos de base 64.

**Sintaxis:** <br> `str ConvertToBase64(str source)`

**Ejemplo:** <br> `ConvertToBase64("Hello world!")` <br> devuelve "SABlAGwAbABvACAAdwBvAHIAbABkACEA"




----------
### ConvertToUTF8Hex

**Descripción:**<br> la función ConvertToUTF8Hex convierte una cadena en un valor codificado hexadecimal UTF8.

**Sintaxis:**<br> `str ConvertToUTF8Hex(str source)`

**Comentarios:**<br> Azure Active Directory utiliza el formato de salida de esta función como formato de atributo DN.

**Ejemplo:** <br> `ConvertToUTF8Hex("Hello world!")` <br> devuelve 48656C6C6F20776F726C6421




----------
### Recuento

**Descripción:**<br> la función Count devuelve el número de elementos en un atributo de varios valores

**Sintaxis:** <br> `num Count(mvstr attribute)`




----------
### CNum

**Descripción:**<br> la función CNum toma una cadena y devuelve un tipo de datos numérico.

**Sintaxis:** <br> `num CNum(str value)`




----------
### CRef

**Descripción:**<br> convierte una cadena en un atributo de referencia

**Sintaxis:** <br> `ref CRef(str value)`

**Ejemplo:** <br> `CRef(“CN=LC Services,CN=Microsoft,CN=lcspool01, CN=Pools,CN=RTC Service,” & %Forest.LDAP%)`




----------
### CStr

**Descripción:**<br> la función CStr se convierte en un tipo de datos de cadena.

**Sintaxis:** <br> `str CStr(num value)` <br> `str CStr(ref value)` <br> `str CStr(bool value)` <br>

- value: puede ser un valor numérico, un atributo de referencia o un valor booleano.

**Ejemplo:** <br> `CStr([dn]) <br>` podría devolver “cn=Joe,dc=contoso,dc=com”




----------
### DateAdd

**Descripción:**<br> devuelve un valor Date que contiene una fecha a la que se ha agregado un intervalo de tiempo especificado.

**Sintaxis:** <br> `dt DateAdd(str interval, num value, dt date)`

- interval: expresión de cadena que es el intervalo de tiempo que desea agregar. La cadena debe tener uno de los siguientes valores:
 - yyyy Año
 - q Trimestre
 - m Mes
 - y Día del año
 - d Día
 - w Día de la semana
 - ww Semana
 - h Hora
 - n Minuto
 - s Segundo
- value: el número de unidades que desea agregar. Puede ser positivo (para obtener fechas futuras) o negativo (para obtener fechas del pasado).
- date: DateTime que representa la fecha a la que se agrega el intervalo.

**Ejemplo:** <br> `DateAdd("m", 3, CDate("2001-01-01"))` <br> agrega 3 meses y devuelve DateTime que representa "2001-04-01”




----------
### DateFromNum

**Descripción:**<br> la función DateFromNum convierte un valor en formato de fecha de AD en un tipo DateTime.

**Sintaxis:** <br> `dt DateFromNum(num value)`

**Ejemplo:** <br> `DateFromNum([lastLogonTimestamp])` <br> `DateFromNum(129699324000000000)` <br> devuelve DateTime que representa 2012-01-01 23:00:00




----------
### DNComponent

**Descripción:** <br> la función DNComponent devuelve el valor de un componente DN especificado a partir de la izquierda.

**Sintaxis:** <br> `str DNComponent(ref dn, num ComponentNumber)`

- dn: el atributo de referencia que interpretar
- ComponentNumber: el componente en DN que devolver

**Ejemplo:** <br> `DNComponent([dn],1)` <br> si dn es “cn=Joe,ou=…, devuelve Joe




----------
### DNComponentRev

**Descripción:** <br> la función DNComponentRev devuelve el valor de un componente DN especificado a partir de la derecha (final).

**Sintaxis:** <br> `str DNComponentRev(ref dn, num ComponentNumber)` <br> `str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn: el atributo de referencia que interpretar
- ComponentNumber: el componente en DN que devolver
- Opciones: DC, ignora todos los componentes con “dc=”

**Ejemplo:** <br> `If dn is “cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com” then DNComponentRev([dn],3)` <br> `DNComponentRev([dn],1,”DC”)` <br> ambos devuelven US.




----------
### Error

**Descripción:** <br> la función Error se usa para devolver un error personalizado.

**Sintaxis:** <br> `void Error(str ErrorMessage)`

**Ejemplo:**<br>`IIF(IsPresent([accountName]),[accountName],Error(“AccountName is required”))`<br> si el atributo accountName no está presente, se produce un error en el objeto.




----------
### EscapeDNComponent

**Descripción:**<br> la función EscapeDNComponent toma un componente de DN y genera secuencias de escape para poderlo representar en LDAP.

**Sintaxis:** <br> `str EscapeDNComponent(str value)`

**Ejemplo:** <br> `EscapeDNComponent(“cn=” & [displayName]) & “,” & %ForestLDAP%` <br> se asegura de que el objeto se puede crear en un directorio LDAP incluso si el atributo displayName tiene caracteres para los que deben generarse secuencias de escape en LDAP.




----------
### FormatDateTime

**Descripción:**<br> la función FormatDateTime se utiliza para dar formato a un valor DateTime en una cadena con un formato especificado

**Sintaxis:** <br> `str FormatDateTime(dt value, str format)`

- value: un valor con el formato DateTime<br>
- format: una cadena que representa el formato de conversión.

**Comentarios:** <br> puede encontrar aquí los valores posibles para el formato: [Formatos de fecha y hora definidos por el usuario (función Format)](http://msdn2.microsoft.com/library/73ctwf33(VS.90).aspx)

**Ejemplo:** <br>

`FormatDateTime(CDate(“12/25/2007”),”yyyy-mm-dd”)` <br> Genera “2007-12-25”.

`FormatDateTime(DateFromNum([pwdLastSet]),”yyyyMMddHHmmss.0Z”)` <br> Puede generar “20140905081453.0Z”




----------
### GUID

**Descripción:** <br> la función GUID genera un nuevo GUID aleatorio

**Sintaxis:** <br> `str GUID()`




----------
### IIF

**Descripción:** <br> la función IIF devuelve uno de un conjunto de valores posibles según una condición especificada.

**Sintaxis:** <br> `var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition: cualquier valor o expresión que pueda evaluarse en True o False.
- valueIfTrue: un valor que se devolverá si se evalúa una condición en True.
- valueIfFalse: un valor que se devolverá si se evalúa una condición en False.

**Ejemplo:** <br> `IIF([employeeType]=“Intern”,”t-“&[alias],[alias])` <br> devuelve el alias de un usuario con “t-“ incluido al principio de él si el usuario está en prácticas. De lo contrario, el alias del usuario se queda como está.




----------
### InStr

**Descripción:** <br> la función InStr busca la primera ocurrencia de una subcadena en una cadena

**Sintaxis:** <br>

`num InStr(str stringcheck, str stringmatch)` <br> `num InStr(str stringcheck, str stringmatch, num start)` <br> `num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck: cadena que se va a buscar <br>
- stringmatch: cadena que se tiene que encontrar <br>
- start: posición de inicio para encontrar la subcadena <br>
- compare: vbTextCompare o vbBinaryCompare

**Comentarios:**<br> devuelve la posición en la que se encontró la subcadena o 0 si no se encuentra.

**Ejemplo:** <br> `InStr("The quick brown fox","quick")` <br> se evalúa en 5

`InStr("repEated","e",3,vbBinaryCompare)` <br> Se evalúa en 7




----------
### InStrRev

**Descripción:** <br> la función InStrRev busca la última ocurrencia de una subcadena en una cadena

**Sintaxis:** <br> `num InstrRev(str stringcheck, str stringmatch)` <br> `num InstrRev(str stringcheck, str stringmatch, num start)` <br> `num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck: cadena que se va a buscar <br>
- stringmatch: cadena que se tiene que encontrar <br>
- start: posición de inicio para encontrar la subcadena <br>
- compare: vbTextCompare o vbBinaryCompare

**Comentarios:** <br> devuelve la posición en la que se encontró la subcadena o 0 si no se encuentra.

**Ejemplo:** <br> `InStrRev("abbcdbbbef","bb")` <br> devuelve 7




----------
### IsBitSet

**Descripción:**<br> la función IsBitSet prueba si se establece un bit o no

**Sintaxis:** <br> `bool IsBitSet(num value, num flag)`

- value: un valor numérico que es evaluated.flag: un valor numérico que tiene el bit que se va a evaluar

**Ejemplo:** <br> `IsBitSet(&HF,4)` <br> devuelve True porque bit “4” se establece en el valor hexadecimal “F”




----------
### IsDate

**Descripción:** <br> la función IsDate se evalúa en True si la expresión se puede evaluar como tipo DateTime.

**Sintaxis:** <br> `bool IsDate(var Expression)`

**Comentarios:** <br> se usa para determinar si CDate() será correcto.




----------
###IsEmpty

**Descripción:** <br> la función IsEmpty se evalúa en True si el atributo está presente en CS o MV pero se evalúa en una cadena vacía.

**Sintaxis:** <br> `bool IsEmpty(var Expression)`




----------
###IsGuid

**Descripción:** <br> la función IsGuid evaluada en True si la cadena se pudo convertir en un GUID.

**Sintaxis:** <br> `bool IsGuid(str GUID)`

**Comentarios:**<br> un GUID se define como una cadena que sigue uno de estos patrones: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx or {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Se usa para determinar si CGuid() es correcto.

**Ejemplo:** <br> `IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` <br> si StrAttribute tiene un formato GUID, devuelve una representación binaria. De lo contrario, devuelve un valor Null.




----------
###IsNull

**Descripción:** <br> la función IsNull devuelve True si la expresión se evalúa como Null.

**Sintaxis:** <br> `bool IsNull(var Expression)`

**Comentarios:** <br> para un atributo, un valor Null se expresa mediante la ausencia del atributo.

**Ejemplo:** <br> `IsNull([displayName])` <br> devuelve True si el atributo no está presente en CS o MV.




----------
###IsNullOrEmpty

**Descripción:** <br> la función IsNullOrEmpty devuelve True si la expresión es Null o una cadena vacía.

**Sintaxis:** <br> `bool IsNullOrEmpty(var Expression)`

**Comentarios:** <br> para un atributo, esto se evaluaría como True si el atributo no está presente o está presente pero es una cadena vacía.<br> La función contraria a esta es IsPresent.

**Ejemplo:**<br>`IsNullOrEmpty([displayName])`<br> devuelve True si el atributo no está presente o si es una cadena vacía en CS o MV.




----------
### IsNumeric

**Descripción:**<br> la función IsNumeric devuelve un valor booleano que indica si una expresión se puede evaluar como un tipo de número.

**Sintaxis:** <br> `bool IsNumeric(var Expression)`

**Comentarios:** <br> se usa para determinar si CNum() analizará correctamente la expresión.




----------
### IsString

**Descripción:** <br> la función IsString se evalúa en True si la expresión se puede evaluar en un tipo de cadena.

**Sintaxis:** <br> `bool IsString(var expression)`

**Comentarios:** <br> se usa para determinar si CStr() analizará correctamente la expresión.




----------
### IsPresent

**Descripción:**<br> la función IsPresent devuelve True si la expresión se evalúa como una cadena que no es Null y no está vacía.

**Sintaxis:** <br> `bool IsPresent(var expression)`

**Comentarios:** <br> la función contraria a esta es IsNullOrEmpty.

**Ejemplo:** <br>

`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`




----------
### Elemento

**Descripción:**<br> la función Item devuelve un elemento de un atributo o una cadena de varios valores.

**Sintaxis:** <br> `var Item(mvstr attribute, num index)`

- attribute: atributo de varios valores <br>
- index: índice para un elemento en la cadena de varios valores.

**Comentarios:** <br> la función Item es útil con la función Contains puesto que esta última función devolverá el índice a un elemento en el atributo de varios valores.

Se produce un error si el índice está fuera de los límites.

**Ejemplo:** <br> `Mid(Item([proxyAddress],Contains([proxyAddress], ”SMTP:”)),6)` <br> devuelve la dirección de correo electrónico principal.




----------
### ItemOrNull

**Descripción:**<br> la función ItemOrNul devuelve un elemento de un atributo o una cadena de varios valores.

**Sintaxis:** <br> `var ItemOrNull(mvstr attribute, num index)`

- attribute: atributo de varios valores <br>
- index: índice para un elemento en la cadena de varios valores.

**Comentarios:** <br> la función ItemOrNull es útil con la función Contains puesto que esta última función devolverá el índice a un elemento en el atributo de varios valores.

Devuelve un valor Null si el índice está fuera de los límites.




----------
### Join

**Descripción:**<br> la función Join toma una cadena de varios valores y devuelve una cadena de valor único con separador especificado insertado entre cada elemento.

**Sintaxis:** <br> `str Join(mvstr attribute)` <br> `str Join(mvstr attribute, str Delimiter)`

- attribute: atributo de varios valores que contiene cadenas para combinar. <br>
- delimiter: cualquier cadena utilizada para separar las subcadenas en la cadena devuelta. Si se omite, se usa el carácter de espacio (""). Si delimitador es una cadena de longitud cero ("") o nada, todos los elementos de la lista se concatenan sin delimitadores.

**Comentarios:** <br> hay paridad entre las funciones Join y Split. La función Join toma una matriz de cadenas y las combina con una cadena de delimitación para devolver una sola cadena. La función Split toma una cadena y la separa en el delimitador para devolver una matriz de cadenas. Sin embargo, una diferencia clave es que Join puede concatenar cadenas con cualquier cadena de delimitación, mientras que attribute puede separar solo cadenas mediante un delimitador de carácter único.

**Ejemplo:** <br> `Join([proxyAddresses],”,”)` <br> podría devolver: “SMTP:john.doe@contoso.com,smtp:jd@contoso.com”




----------
### LCase

**Descripción:**<br> la función LCase convierte todos los caracteres de una cadena a minúsculas.

**Sintaxis:** <br> `str LCase(str value)`

**Ejemplo:** <br> `LCase(“TeSt”)` <br> devuelve “test”.




----------
### Left

**Descripción:** <br> la función Left devuelve un número especificado de caracteres desde la izquierda de una cadena.

**Sintaxis:** <br> `str Left(str string, num NumChars)`

- string: la cadena desde la que devolver los caracteres <br>
- NumChars: un número que identifica el número de caracteres que devolver desde el principio (izquierdo) de la cadena

**Comentarios:** <br> una cadena que contiene los primeros caracteres numChars de la cadena:

- Con numChars = 0, se devuelve una cadena vacía.
- Con numChars < 0, se devuelve una cadena de entrada.
- Si la cadena es null, se devuelve una cadena vacía.

Si la cadena contiene menos caracteres que el número especificado en numChars, se devuelve una cadena idéntica a la cadena (es decir, que contiene todos los caracteres en el parámetro 1).

**Ejemplo:** <br> `Left(“John Doe”, 3)` <br> devuelve “Joh”.




----------
### Len

**Descripción:** <br> la función Len devuelve el número de caracteres en una cadena.

**Sintaxis:** <br> `num Len(str value)`

**Ejemplo:** <br> `Len(“John Doe”)` <br> devuelve 8.




----------
### LTrim

**Descripción:** <br> la función LTrim quita los espacios en blanco del principio de una cadena.

**Sintaxis:** <br> `str LTrim(str value)`

**Ejemplo:** <br> `LTrim(“ Test ”)` <br> devuelve “Test”.




----------
### Mid

**Descripción:** <br> la función Mid devuelve un número especificado de caracteres desde una posición especificada en una cadena.

**Sintaxis:** <br> `str Mid(str string, num start, num NumChars)`

- string: la cadena desde la que devolver los caracteres <br>
- start: un número que identifica la posición de inicio en una cadena desde la que devolver los caracteres
- NumChars: un número que identifica el número de caracteres que devolver desde la posición en una cadena


**Comentarios:** <br> devuelve caracteres numChars que comienzan por la posición de inicio en una cadena.<br> Una cadena que contiene caracteres numChars de la posición de inicio en una cadena:

- Con numChars = 0, se devuelve una cadena vacía.
- Con numChars < 0, se devuelve una cadena de entrada.
- Con start >, la longitud de la cadena devuelve la cadena de entrada.
- Con start <= 0, se devuelve la cadena de entrada.
- Si la cadena es null, se devuelve una cadena vacía.

Si no hay caracteres numChar restantes en la cadena de la posición de inicio, se devuelve el máximo de caracteres que puede devolverse.

**Ejemplo:** <br>

`Mid(“John Doe”, 3, 5)` <br> Devuelve “hn Do”.

`Mid(“John Doe”, 6, 999)` <br> Devuelve “Doe”




----------
### Now

**Descripción:** <br> la función Now devuelve DateTime que especifica la fecha y hora actuales, según la fecha y hora del sistema del equipo.

**Sintaxis:** <br> `dt Now()`




----------
### NumFromDate

**Descripción:** <br> la función NumFromDate devuelve una fecha en formato de fecha de AD.

**Sintaxis:** <br> `num NumFromDate(dt value)`


**Ejemplo:** <br> `NumFromDate(CDate("2012-01-01 23:00:00"))` <br> devuelve 129699324000000000.




----------
### PadLeft

**Descripción:** <br> la función PadLeft rellena en la parte izquierda una cadena con una longitud especificada mediante un carácter controlador proporcionado.

**Sintaxis:** <br> `str PadLeft(str string, num length, str padCharacter)`

- string: la cadena que rellenar. <br>
- length: un número entero que representa la longitud deseada de la cadena. <br>
- padCharacter: una cadena que consta de un solo carácter que se usará como el carácter controlador



----------
### Comentarios

- Si la longitud de cadena es inferior a la longitud, padCharacter se anexa repetidamente al principio (izquierda) de la cadena hasta que tenga una longitud igual a la longitud.
- PadCharacter puede ser un carácter de espacio, pero no puede ser un valor Null.
- Si la longitud de cadena es igual o mayor que la longitud, la cadena se devuelve sin cambios.
- Si la cadena tiene una longitud mayor que o igual que la longitud, se devuelve una cadena idéntica a la cadena.
- Si la longitud de cadena es menor que la longitud, se devuelve una cadena nueva de la longitud deseada que contiene una cadena rellenada con un padCharacter.
- Si la cadena es Null, la función devuelve una cadena vacía.

**Ejemplo:** <br> `PadLeft(“User”, 10, “0”)` <br> devuelve “000000User”.




----------
### PadRight

**Descripción:** <br> la función PadRight rellena en la parte derecha una cadena con una longitud especificada mediante un carácter controlador proporcionado.

**Sintaxis:** <br> `str PadRight(str string, num length, str padCharacter)`

- string: la cadena que rellenar.
- length: un número entero que representa la longitud deseada de la cadena.
- padCharacter: una cadena que consta de un solo carácter que se usará como el carácter controlador

**Comentarios:**

- Si la longitud de cadena es inferior a la longitud, padCharacter se anexa repetidamente al final (derecha) de la cadena hasta que tenga una longitud igual a la longitud.
- padCharacter puede ser un carácter de espacio, pero no puede ser un valor Null.
- Si la longitud de cadena es igual o mayor que la longitud, la cadena se devuelve sin cambios.
- Si la cadena tiene una longitud mayor que o igual que la longitud, se devuelve una cadena idéntica a la cadena.
- Si la longitud de cadena es menor que la longitud, se devuelve una cadena nueva de la longitud deseada que contiene una cadena rellenada con un padCharacter.
- Si la cadena es Null, la función devuelve una cadena vacía.


**Ejemplo:** <br> `PadRight(“User”, 10, “0”)` <br> devuelve “User000000”.




----------
### PCase

**Descripción:**<br> la función PCase convierte el primer carácter de cada palabra delimitada por espacios de una cadena a mayúsculas, y todos los demás caracteres se convierten a minúsculas.

**Sintaxis:** <br> `String PCase(string)`

**Ejemplo:** <br> `PCase(“TEsT”)` <br> devuelve “Test”.




----------
### RandomNum

**Descripción:** <br> la función RandomNum devuelve un número aleatorio entre un intervalo especificado.

**Sintaxis:** <br> `num RandomNum(num start, num end)`

- start: un número que identifica el límite inferior del valor aleatorio que se va a generar <br>
- end: un número que identifica el límite superior del valor aleatorio que generar

**Ejemplo:** <br> `Random(100,999)` <br> devuelve 734.




----------
### RemoveDuplicates

**Descripción:** <br> la función RemoveDuplicates toma una cadena de varios valores y garantiza que cada valor es único.

**Sintaxis:** <br> `mvstr RemoveDuplicates(mvstr attribute)`

**Ejemplo:** <br> `RemoveDuplicates([proxyAddresses])` <br> devuelve un atributo proxyAddress saneado donde se han quitado todos los valores duplicados.




----------
### Sustituya

**Descripción:** <br> la función Replace reemplaza todas las apariciones de una cadena en otra cadena.

**Sintaxis:** <br> `str Replace(str string, str OldValue, str NewValue)`

- string: una cadena en la que reemplazar los valores. <br>
- OldValue: la cadena que se va a buscar y reemplazar. <br>
- NewValue: la cadena que reemplazar.


**Comentarios:** <br> la función reconoce los siguientes monikers especiales:

- \\n: nueva línea
- \\r: retorno de carro
- \\t: tabulación


**Ejemplo:** <br>

`Replace([address],”\r\n”,”, “)`<br> Reemplaza CRLF por una coma y un espacio y podría originar “One Microsoft Way, Redmond, WA, USA”




----------
### ReplaceChars

**Descripción:**<br> la función ReplaceChars reemplaza todas las apariciones de caracteres encontrados en la cadena ReplacePattern.

**Sintaxis:** <br> `str ReplaceChars(str string, str ReplacePattern)`

- string: una cadena por la que reemplazar los caracteres.
- ReplacePattern: una cadena que contiene un diccionario con caracteres que reemplazar.

El formato es {source1}:{target1},{source2}:{target2},{sourceN},{targetN} donde source es el carácter que buscar y target la cadena por la que reemplazar.


**Comentarios:**

- La función toma cada aparición de los orígenes definidos y los reemplaza por los destinos.
- El origen debe ser exactamente un carácter (unicode).
- El origen no puede estar vacío ni puede tener más de un carácter (error de análisis).
- El destino puede tener varios caracteres, por ejemplo, ö:oe, β:ss.
- El destino puede estar vacío, lo que indica que el carácter debe quitarse.
- El origen distingue mayúsculas y minúsculas, y debe ser una coincidencia exacta.
- La coma (,) y los dos puntos (:) son caracteres reservados y no se puede reemplazar utilizando esta función.
- Se omiten los espacios y otros caracteres en blanco en la cadena ReplacePattern.


**Ejemplo:** <br> '%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o'

`ReplaceChars(”Räksmörgås”,%ReplaceString%)` <br> Devuelve Raksmorgas

`ReplaceChars(“O’Neil”,%ReplaceString%)` <br> Devuelve “ONeil”, se define la marca única para quitarla.




----------
### Right

**Descripción:** <br> la función Right devuelve un número especificado de caracteres desde la derecha (final) de una cadena.

**Sintaxis:** <br> `str Right(str string, num NumChars)`

- string: la cadena desde la que devolver los caracteres
- NumChars: un número que identifica el número de caracteres que devolver desde el final (derecho) de la cadena

**Comentarios:** <br> los caracteres NumChars se devuelven a partir de la última posición de la cadena.

Una cadena que contiene los últimos caracteres numChars de la cadena:

- Con numChars = 0, se devuelve una cadena vacía.
- Con numChars < 0, se devuelve una cadena de entrada.
- Si la cadena es null, se devuelve una cadena vacía.

Si la cadena contiene menos caracteres que el número especificado en NumChars, se devuelve una cadena idéntica a la cadena.

**Ejemplo:** <br> `Right(“John Doe”, 3)` <br> devuelve “Doe”.




----------
### RTrim

**Descripción:** <br> la función RTrim quita los espacios en blanco del final de una cadena.

**Sintaxis:** <br> `str RTrim(str value)`

**Ejemplo:** <br> `RTrim(“ Test ”)` <br> devuelve “Test”.




----------
### Dividir

**Descripción:** <br> la función Split toma una cadena separada por un delimitador y la convierte en una cadena de varios valores.


**Sintaxis:** <br> `mvstr Split(str value, str delimiter)` <br? `mvstr Split(str value, str delimiter, num limit)`

- value: la cadena con un carácter delimitador que separar.
- delimiter: carácter único que usar como delimitador.
- limit: número máximo de valores que se devolverá.

**Ejemplo:** <br> `Split(“SMTP:john.doe@contoso.com,smtp:jd@contoso.com”,”,”)` <br> devuelve una cadena de varios valores con 2 elementos útiles para el atributo proxyAddress




----------
### StringFromGuid

**Descripción:**<br> la función StringFromGuid toma un GUID binario y lo convierte en una cadena

**Sintaxis:** <br> `str StringFromGuid(bin GUID)`




----------
### StringFromSid

**Descripción:** <br> la función StringFromSid convierte una matriz de bytes o una matriz de bytes de varios valores que contiene un identificador de seguridad en una cadena o en una cadena de varios valores.

**Sintaxis:** <br> `str StringFromSid(bin ObjectSID)` <br> `mvstr StringFromSid(mvbin ObjectSID)`




----------
### Switch

**Descripción:** <br> la función Switch se utiliza para devolver un único valor según las condiciones evaluadas.

**Sintaxis:** <br> `var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: expresión variante que desea evaluar.
- value: valor que se va a devolver si la expresión correspondiente es True.

**Comentarios:**<br> la lista de argumentos de la función Switch consta de pares de expresiones y valores. Las expresiones se evalúan de izquierda a derecha y se devuelve el valor asociado a la primera expresión que evaluar en True. Si los elementos no están emparejados correctamente, se produce un error en tiempo de ejecución.

Por ejemplo, si expr1 es True, Switch devuelve value1. Si expr-1 es False, pero expr-2 es True, Switch devuelve value-2, etc.

Switch no devuelve nada si: -Ninguna de las expresiones es True. -La primera expresión True tiene un valor correspondiente que es Null.

Switch evalúa todas las expresiones, aunque devuelva solo una de ellas. Por este motivo, debe vigilar efectos secundarios no deseados. Por ejemplo, si la evaluación de cualquier expresión tiene como resultado una división por cero, se produce un error.

El valor puede ser también la función Error que devolvería una cadena personalizada.

**Ejemplo:** <br> `Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error(“Unknown city”))` <br> devuelve el idioma hablado en las ciudades más importantes. De lo contrario, devuelve un error.




----------
### Trim

**Descripción:** <br> la función Trim quita los espacios en blanco del principio y del final de una cadena.

**Sintaxis:** <br> `str Trim(str value)` <br> `mvstr Trim(mvstr value)`

**Ejemplo:** <br> `Trim(“ Test ”)` <br> devuelve “Test”.

`Trim([proxyAddresses])` <br> Quita los espacios del principio y del final de cada valor en el atributo proxyAddress.




----------
### UCase

**Descripción:** <br> la función UCase convierte todos los caracteres de una cadena a mayúsculas.

**Sintaxis:** <br> `str UCase(str string)`

**Ejemplo:** <br> `UCase(“TeSt”)` <br> devuelve “TEST”.




----------
### Word

**Descripción:** <br> la función Word devuelve una palabra incluida en una cadena, según los parámetros que describen los delimitadores que usar y el número de palabras que devolver.

**Sintaxis:** <br> `str Word(str string, num WordNumber, str delimiters)`

- string: la cadena desde la que devolver una palabra.
- WordNumber: un número que identifica qué número de palabras debe devolverse.
- delimiters: una cadena que representa los delimitadores que deben usarse para identificar palabras

**Comentarios:** <br> cada cadena de caracteres en la cadena separada por uno de los caracteres en los delimitadores se identifica como palabras:

- Si el número es < 1, se devuelve una cadena vacía.
- Si la cadena es Null, se devuelve una cadena vacía.

Si la cadena contiene menos palabras o si la cadena no contiene palabras identificadas por los delimitadores, se devuelve una cadena vacía.


**Ejemplo:** <br> `Word(“The quick brown fox”,3,” “)` <br> Devuelve “brown”

`Word(“This,string!has&many seperators”,3,”,!&#”)` <br> devolvería “has”


## Recursos adicionales

* [Explicación de las expresiones declarativas de aprovisionamiento](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)


<!--Image references-->

<!---HONumber=AcomDC_0218_2016-->