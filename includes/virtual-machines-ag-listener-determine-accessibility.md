Es importante saber que hay dos formas de configurar un agente de escucha del grupo de disponibilidad en Azure. Estos métodos se diferencian en el tipo de equilibrador de carga de Azure que utilizas al crear el agente de escucha. La siguiente tabla muestra las diferencias:

| Equilibrador de carga | Implementación | Cuándo utilizarlo |
| ------------- | -------------- | ----------- |
| **Externo** | Usa la **dirección IP Virtual pública** del servicio en la nube que hospeda las máquinas virtuales. | Cuando necesites tener acceso al agente de escucha del exterior de la red virtual, incluso desde Internet. |
| **Interno** | Usa el **Equilibrio de carga interno (ILB)** con una dirección privada del agente de escucha. | Cuando solo accedes al agente de escucha desde la misma red virtual. Esto incluye la VPN de sitio a sitio en escenarios híbridos. |

>[AZURE.IMPORTANT]Para el caso de un agente de escucha que utilice el VIP público (equilibrador de carga externo) del servicio en la nube, cualquier dato devuelto por el agente de escucha se considera de salida y se cobra según las tarifas normales de transferencia de datos de Azure. Esto ocurre incluso si el cliente se encuentra en la misma red virtual y centro de datos que el agente de escucha y las bases de datos. Pero no es así para un agente de escucha que esté utilizando el ILB.

El ILB solo se puede configurar en redes virtuales con un ámbito regional. Las redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar ILB. Para obtener más información, consulta el tema [Equilibrador de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

<!------HONumber=Oct15_HO3-->