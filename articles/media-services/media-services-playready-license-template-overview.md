<properties 
	pageTitle="Información general de plantillas de licencias de PlayReady de Servicios multimedia" 
	description="Este tema proporciona información general sobre la plantilla de licencia de PlayReady que se usó para configurar las licencias de PlayReady." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Información general de plantillas de licencias de PlayReady de Servicios multimedia

Servicios multimedia de Azure proporciona un servicio para entregar licencias de Microsoft PlayReady. Cuando el reproductor del usuario final (por ejemplo, Silverlight) intenta reproduce el contenido protegido de PlayReady, se envía una solicitud al servicio de entrega de licencias para obtener una licencia. Si el servicio de licencia aprueba la solicitud, emite la licencia, la que se envía al cliente y se puede usar para descifrar y reproducir el contenido especificado.

Servicios multimedia también proporciona API que permiten configurar sus licencias de PlayReady. Las licencias contienen los derechos y restricciones que desea para el tiempo de ejecución de DRM de PlayReady con la finalidad de hacer respetar los requisitos cuando un usuario intenta reproducir contenido protegido. A continuación se muestran algunos ejemplos de restricciones de licencia de PlayReady que puede especificar:

- Fecha y hora desde la que la licencia es válida.
- Fecha y hora cuando caduca la licencia. 
- Para que la licencia se guarde en un almacenamiento persistente en el cliente. Las licencias permanentes se usan normalmente para permitir la reproducción sin conexión de contenido.
- El nivel de seguridad mínimo que debe tener un reproductor para reproducir contenido. 
- El nivel de protección de salida de los controles de salida para el contenido de audio y video. 
- Para obtener más información, consulte la sección Controles de salida (3.5) en el documento [Reglas de cumplimiento normativo de PlayReady](https://www.microsoft.com/playready/licensing/compliance/).

>[AZURE.NOTE]Actualmente, solo puede configurar el derecho de reproducción PlayRight de la licencia de PlayReady (este derecho es necesario). PlayRight da al cliente la capacidad para reproducir el contenido. PlayRight también permite configurar restricciones específicas para la reproducción. Para obtener más información, consulte [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight).

Para configurar licencias de PlayReady con Servicios multimedia, debe configurar la plantilla de licencias de PlayReady de Servicios multimedia. La plantilla se define en XML.

En el ejemplo siguiente se muestra la plantilla más sencilla (y común) que configura una licencia básica de streaming. Con esta licencia, los clientes podrán reproducir contenido protegido con PlayReady.
	
	<?xml version="1.0" encoding="utf-8"?>
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" 
	                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <PlayRight />
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

El código XML se ajusta al esquema XML de la plantilla de licencias de PlayReady definido en la sección de esquema XML de la plantilla de licencias de PlayReady.

Servicios multimedia también define un conjunto de clases de .NET que podría usarse para serializar y deserializar hacia y desde XML. Para obtener una descripción de las clases principales, consulte [Clases .NET de Servicios multimedia]((media-services-playready-license-template-overview.md#classes). que se usan para configurar las plantillas de licencia.

Para ver un ejemplo completo que usa clases .NET para configurar la plantilla de licencia de PlayReady, consulte [Uso del cifrado dinámico de PlayReady y servicio de entrega de licencias](https://msdn.microsoft.com/library/azure/dn783467.aspx).

##<a id="classes"></a>Clases .NET de Servicios multimedia que se usan para configurar las plantillas de licencia

Las siguientes son las clases principales de .NET se usan para configurar las plantillas de licencia de PlayReady de Servicios multimedia. Estas clases se asignan a los tipos definidos en [Esquema XML de plantilla de licencia de PlayReady](media-services-playready-license-template-overview.md#schema).

La clase [MediaServicesLicenseTemplateSerializer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer.aspx) se usa para serializar y deserializar hacia y desde el código XML de la plantilla de licencia de Servicios multimedia.

###PlayReadyLicenseResponseTemplate

[PlayReadyLicenseResponseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate.aspx): esta clase representa la plantilla para la respuesta que se devuelve al usuario final. Contiene un campo para una cadena de datos personalizada entre el servidor de licencias y la aplicación (puede ser útil para la lógica de aplicación personalizada), así como una lista de una o más plantillas de licencia.

Esta es la clase de "nivel superior" en la jerarquía de plantillas. Lo que significa que la plantilla de respuesta incluye una lista de plantillas de licencia y las plantillas de licencia incluyen (directa o indirectamente) todas las demás clases que conforman los datos de la plantilla que se va a serializar.


###PlayReadyLicenseTemplate

[PlayReadyLicenseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate.aspx): la clase representa una plantilla de licencia para crear licencias de PlayReady que se devuelven a los usuarios finales. Contiene los datos de la clave de contenido de la licencia y los derechos o restricciones que el tiempo de ejecución DRM de PlayReady debe aplicar cuando use la clave de contenido.


###<a id="PlayReadyPlayRight"></a>PlayReadyPlayRight

[PlayReadyPlayRight](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright.aspx): esta clase representa el derecho PlayRight de una licencia de PlayReady. Concede al usuario la capacidad de reproducir contenido sujeto a las restricciones configuradas en la licencia y en el propio derecho PlayRight (para la directiva de reproducción específica). En lo referente a PlayRight, gran parte de la directiva tiene que ver con las restricciones de salida que controlan los tipos de salida sobre los que se puede reproducir el contenido y las restricciones que se deben aplicar cuando se usa una salida determinada. Por ejemplo, si DigitalVideoOnlyContentRestriction está habilitada, el tiempo de ejecución DRM solo permitirá que el vídeo se muestre mostrarse en salidas digitales (las salidas de vídeo analógicas no podrán pasar el contenido).

>[AZURE.IMPORTANT]Estos tipos de restricciones pueden ser muy eficaces, pero también pueden afectar a la experiencia del consumidor. Si las protecciones de salida son demasiado restrictivas, puede que el contenido no se reproduzca en algunos clientes. Para más información, consulte el documento [PlayReady Compliance Rules](https://www.microsoft.com/playready/licensing/compliance/).

Para ver un ejemplo de los niveles de protección que admite Silverlight, consulte: [Silverlight support for output protections](http://go.microsoft.com/fwlink/?LinkId=617318).

##<a id="schema"></a>Esquema XML de la plantilla de licencia de PlayReady
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
	  <xs:complexType name="AgcAndColorStripeRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
	  <xs:simpleType name="ContentType">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="Unspecified" />
	      <xs:enumeration value="UltravioletDownload" />
	      <xs:enumeration value="UltravioletStreaming" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
	  <xs:complexType name="ExplicitAnalogTelevisionRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
	  <xs:complexType name="PlayReadyContentKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
	  <xs:complexType name="ContentEncryptionKeyFromHeader">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:PlayReadyContentKey">
	        <xs:sequence />
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
	  <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:PlayReadyContentKey">
	        <xs:sequence>
	          <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
	  <xs:complexType name="PlayReadyLicenseResponseTemplate">
	    <xs:sequence>
	      <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
	      <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
	  <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
	  <xs:complexType name="PlayReadyLicenseTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
	      <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
	      <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
	      <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
	  <xs:simpleType name="PlayReadyLicenseType">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="Nonpersistent" />
	      <xs:enumeration value="Persistent" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
	  <xs:complexType name="PlayReadyPlayRight">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	      <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
	        <xs:annotation>
	          <xs:appinfo>
	            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
	          </xs:appinfo>
	        </xs:annotation>
	      </xs:element>
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
	  <xs:simpleType name="UnknownOutputPassingOption">
	    <xs:restriction base="xs:string">
	      <xs:enumeration value="NotAllowed" />
	      <xs:enumeration value="Allowed" />
	      <xs:enumeration value="AllowedWithVideoConstriction" />
	    </xs:restriction>
	  </xs:simpleType>
	  <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
	  <xs:complexType name="ScmsRestriction">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
	</xs:schema>



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->