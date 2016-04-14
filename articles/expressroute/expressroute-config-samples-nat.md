<properties
   pageTitle="Ejemplos de configuración de enrutadores de cliente ExpressRoute | Microsoft Azure"
   description="Esta página ofrece ejemplos de configuración de enrutamiento para enrutadores Cisco y Juniper."
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

# Ejemplos de configuración de enrutadores para configurar y administrar NAT

Esta página proporciona ejemplos de configuración de NAT para enrutadores Cisco serie ASA y Juniper serie MX. Solo pretenden ser ejemplos de carácter informativo y no se deben usar tal cual. Puede trabajar con el proveedor para elaborar las configuraciones adecuadas para la red.

>[AZURE.IMPORTANT]Los ejemplos de esta página pretenden tener un carácter meramente informativo. Debe trabajar con los equipos técnico y de ventas del proveedor y su equipo de red para elaborar las configuraciones adecuadas que satisfagan sus necesidades. Microsoft no dará soporte técnico en problemas relacionados con las configuraciones que aparecen en esta página. Debe ponerse en contacto con el fabricante del dispositivo para problemas de soporte técnico.

Los ejemplos de configuración de enrutadores siguientes se aplican a emparejamientos públicos de Azure y Microsoft. No debe configurar NAT para emparejamiento privado de Azure. Si desea más información, consulte [Emparejamientos de ExpressRoute](expressroute-circuit-peerings.md) y [Requisitos de NAT de ExpressRoute](expressroute-nat.md).

**Nota:** tiene que usar grupos de direcciones IP de NAT independientes para la conectividad a internet y ExpressRoute. Si usa el mismo grupo de direcciones IP de NAT a través de Internet y ExpressRoute se traducirá en enrutamiento asimétrico y pérdida de conectividad.

## Firewalls de Cisco ASA

### Configuración de PAT para el tráfico de red de cliente a Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### Configuración de PAT para el tráfico de Microsoft a la red de cliente

#### Interfaces y dirección:
	Source Interface (where the traffic enters the ASA): inside
	Destination Interface (where the traffic exits the ASA): outside

#### Configuración:
Grupo NAT:

	object network outbound-PAT
		host <NAT-IP>

Servidor de destino:

	object network Customer-Network
		network-object <IP> <Subnet-Mask>

Grupo de objetos de direcciones IP de cliente

	object-group network MSFT-Network-1
		network-object <MSFT-IP> <Subnet-Mask>
	
	object-group network MSFT-PAT-Networks
		network-object object MSFT-Network-1

Comandos NAT:

	nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## Enrutadores Juniper serie MX 

### 1\. Creación de interfaces Ethernet redundantes para el clúster

	interfaces {
	    reth0 {
	        description "To Internal Network";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 1;
	        }
	        unit 100 {
	            vlan-id 100;
	            family inet {
	                address <IP-Address/Subnet-mask>;
	            }
	        }
	    }
	    reth1 {
	        description "To Microsoft via Edge Router";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 2;
	        }
	        unit 100 {
	            description "To Microsoft via Edge Router";
	            vlan-id 100;
	            family inet {
	                address <IP-Address/Subnet-mask>;
	            }
	        }
	    }
	}


### 2\. Creación de dos zonas de seguridad

 - Zona de confianza para la red interna y Zona de no confianza para la red externa que soporta enrutadores de borde
 - Asignación de interfaces adecuadas a las zonas
 - Autorización de servicios en las interfaces


	seguridad { zones { security-zone Trust { host-inbound-traffic { system-services { ping; } protocolos { bgp; } } interfaces { reth0.100; } } security-zone Untrust { host-inbound-traffic { system-services { ping; } protocolos { bgp; } } interfaces { reth1.100; } } } }


### 3\. Creación de directivas de seguridad entre zonas
 
	security {
	    policies {
	        from-zone Trust to-zone Untrust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	        from-zone Untrust to-zone Trust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	    }
	}


### 4\. Configuración de directivas de NAT
 - Creación de dos grupos de NAT. Uno se utilizará para el tráfico saliente de NAT a Microsoft y otro de Microsoft al cliente.
 - Creación de reglas para NAT del tráfico correspondiente

		security {
		    nat {
		        source {
		            pool SNAT-To-ExpressRoute {
		                routing-instance {
		                    External-ExpressRoute;
		                }
		                address {
		                    <NAT-IP-address/Subnet-mask>;
		                }
		            }
		            pool SNAT-From-ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT-IP-address/Subnet-mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External-ExpressRoute;
		                rule SNAT-Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT-To-ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound-NAT {
		                from routing-instance External-ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT-In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT-From-ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		        }
		    }
		}


### 5\. Configuración de BGP para anunciar prefijos selectivos en cada dirección

Consulte ejemplos en la página [Ejemplos de configuración de enrutamiento ](expressroute-config-samples-routing.md).

### 6\. Creación de directivas

	routing-options {
	    	      autonomous-system <Customer-ASN>;
	}
	policy-options {
	    prefix-list Microsoft-Prefixes {
	        <IP-Address/Subnet-Mask;
	        <IP-Address/Subnet-Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise-NAT-Pools {
	        from {
	            protocol static;
	            route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept-from-Microsoft {
	        term 1 {
	            from {
	                instance External-ExpressRoute;
	                prefix-list-filter Microsoft-Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept-from-Internal {
	        term no-private {
	            from {
	                instance Internal;
	                prefix-list-filter private-ranges orlonger;
	            }
	            then reject;
	        }
	        term bgp {
	            from {
	                instance Internal;
	                protocol bgp;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	}
	routing-instances {
	    Internal {
	        instance-type virtual-router;
	        interface reth0.100;
	        routing-options {
	            static {
	                route <NAT-Pool-IP-Address/Subnet-mask> discard;
	            }
	            instance-import Accept-from-Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise-NAT-Pools>;
	                    peer-as <Customer-ASN-1>;
	                    neighbor <BGP-Neighbor-IP-Address>;
	                }
	            }
	        }
	    }
	    External-ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT-Pool-IP-Address/Subnet-mask> discard;
	            }
	            instance-import Accept-from-Internal;
	        }
	        protocols {
	            bgp {
	                group edge-router {
	                    export <Advertise-NAT-Pools>;
	                    peer-as <Customer-Public-ASN>;
	                    neighbor <BGP-Neighbor-IP-Address>;
	                }
	            }
	        }
	    }
	}

## Pasos siguientes

Consulte [P+F de ExpressRoute](expressroute-faqs.md) para obtener más detalles.

<!---HONumber=AcomDC_0121_2016-->