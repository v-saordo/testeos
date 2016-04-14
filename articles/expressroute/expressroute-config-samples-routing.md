<properties
   pageTitle="Ejemplos de configuración de enrutadores de cliente ExpressRoute | Microsoft Azure"
   description="Esta página ofrece ejemplos de configuración de enrutador para enrutadores Cisco y Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Ejemplos de configuración de enrutadores para configurar y administrar enrutamiento

Esta página ofrece ejemplos de configuración de enrutamiento e interfaces para enrutadores Cisco serie IOS-XE y Juniper serie MX. Solo pretenden ser ejemplos de carácter informativo y no se deben usar tal cual. Puede trabajar con el proveedor para elaborar las configuraciones adecuadas para la red.

>[AZURE.IMPORTANT]Los ejemplos de esta página pretenden tener un carácter meramente informativo. Debe trabajar con los equipos técnico y de ventas del proveedor y su equipo de red para elaborar las configuraciones adecuadas que satisfagan sus necesidades. Microsoft no dará soporte técnico en problemas relacionados con las configuraciones que aparecen en esta página. Debe ponerse en contacto con el fabricante del dispositivo para problemas de soporte técnico.

Los ejemplos de configuración de enrutadores siguientes se aplican a todos los emparejamientos. Si desea más información, vea [Emparejamientos de ExpressRoute](expressroute-circuit-peerings.md) y [Requisitos de NAT de ExpressRoute](expressroute-routing.md).

## Enrutadores basados en Cisco IOS-XE

Los ejemplos en esta sección se aplican a cualquier enrutador que ejecute la familia del SO IOS-XE.

### 1\. Configuración de interfaces y subinterfaces

Necesitará una subinterfaz por emparejamiento en cada enrutador que conecte a Microsoft. Una subinterfaz puede identificarse con un identificador de VLAN o un par apilado de identificadores de VLAN y una dirección IP.

#### Definición de interfaz Dot1Q

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con un solo identificador de VLAN. El identificador de VLAN es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### Definición de interfaz QinQ

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con dos identificadores de VLAN. El identificador de VLAN externo (s-tag), si se usa, es el mismo en todos los emparejamientos. El identificador de VLAN interno (c-tag) es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### 2\. Configuración de sesiones eBGP

Debe configurar una sesión BGP con Microsoft para cada emparejamiento. En el siguiente ejemplo permite configurar una sesión BGP con Microsoft. Si la dirección IPv4 usada para la subinterfaz fue a.b.c.d, la dirección IP del vecino BGP (Microsoft) será a.b.c.d+1. El último octeto de la dirección IPv4 del vecino BGP siempre será un número par.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	 neighbor <IP#2_used_by_Azure> activate
	 exit-address-family
	!

### 3\. Configuración de prefijos para anunciarse a través de la sesión BGP

Puede configurar el enrutador para anunciar prefijos seleccionados a Microsoft. Puede hacerlo usando el ejemplo siguiente.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	  network <Prefix_to_be_advertised> mask <Subnet_mask>
	  neighbor <IP#2_used_by_Azure> activate
	 exit-address-family
	!

### 4\. Asignaciones de ruta

Puede usar asignaciones de ruta y listas de prefijo para filtrar prefijos propagados en la red. Puede usar el ejemplo siguiente para realizar la tarea. Asegúrese de que tiene el programa de instalación de listas de prefijos adecuado.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	  network <Prefix_to_be_advertised> mask <Subnet_mask>
	  neighbor <IP#2_used_by_Azure> activate
	  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
	 exit-address-family
	!
	route-map <MS_Prefixes_Inbound> permit 10
	 match ip address prefix-list <MS_Prefixes>
	!


## Enrutadores Juniper serie MX 

Los ejemplos en esta sección se aplican a los enrutadores Juniper serie MX.

### 1\. Configuración de interfaces y subinterfaces

#### Definición de interfaz Dot1Q

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con un solo identificador de VLAN. El identificador de VLAN es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

    interfaces {
    	vlan-tagging;
    	<Interface_Number> {
    		unit <Number> {
    			vlan-id <VLAN_ID>;
    			family inet {
    				address <IPv4_Address/Subnet_Mask>;
    			}
    		}
    	}
    }


#### Definición de interfaz QinQ

Este ejemplo ofrece la definición de subinterfaz de una subinterfaz con dos identificadores de VLAN. El identificador de VLAN externo (s-tag), si se usa, es el mismo en todos los emparejamientos. El identificador de VLAN interno (c-tag) es único para cada emparejamiento. El último octeto de la dirección IPv4 siempre será un número impar.

	interfaces {
	    <Interface_Number> {
	        flexible-vlan-tagging;
	        unit <Number> {
	            vlan-tags outer <S-tag> inner <C-tag>;
	            family inet {
	                address <IPv4_Address/Subnet_Mask>;
	            }                           
	        }                               
	    }                                   
	}                           

### 2\. Configuración de sesiones eBGP

Debe configurar una sesión BGP con Microsoft para cada emparejamiento. En el siguiente ejemplo permite configurar una sesión BGP con Microsoft. Si la dirección IPv4 usada para la subinterfaz fue a.b.c.d, la dirección IP del vecino BGP (Microsoft) será a.b.c.d+1. El último octeto de la dirección IPv4 del vecino BGP siempre será un número par.

	routing-options {
	    autonomous-system <Customer_ASN>;
	}
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}

### 3\. Configuración de prefijos para anunciarse a través de la sesión BGP

Puede configurar el enrutador para anunciar prefijos seleccionados a Microsoft. Puede hacerlo usando el ejemplo siguiente.

	policy-options {
	    policy-statement <Policy_Name> {
	        term 1 {
	            from protocol OSPF;
		route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
	            then {
	                accept;
	            }
	        }
	    }
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            export <Policy_Name>
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}


### 4\. Asignaciones de ruta

Puede usar asignaciones de ruta y listas de prefijo para filtrar prefijos propagados en la red. Puede usar el ejemplo siguiente para realizar la tarea. Asegúrese de que tiene el programa de instalación de listas de prefijos adecuado.

	policy-options {
	    prefix-list MS_Prefixes {
	        <IP_Prefix_1/Subnet_Mask>;
	        <IP_Prefix_2/Subnet_Mask>;
	    }
	    policy-statement <MS_Prefixes_Inbound> {
	        term 1 {
	            from {
		prefix-list MS_Prefixes;
	            }
	            then {
	                accept;
	            }
	        }
	    }
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            export <Policy_Name>
	            import <MS_Prefixes_Inbound>
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}

## Pasos siguientes

Consulte [P+F de ExpressRoute](expressroute-faqs.md) para obtener más detalles.

<!---HONumber=AcomDC_0121_2016-->