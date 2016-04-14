Hay tres SKU de puerta de enlace de VPN:

- Básica
- Standard
- Alto rendimiento

La tabla siguiente muestra los tipos de puerta de enlace y el rendimiento agregado estimado. Los precios difieren entre las SKU de puerta de enlace. Para obtener información acerca de los precios, consulte [Precios de puertas de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabla se aplica a los modelos de implementación del Administrador de recursos y clásico.


| | **Rendimiento de puerta de enlace de VPN** | **Túneles IPsec máx. de puerta de enlace de VPN** | **Rendimiento de puerta de enlace de ExpressRoute** | **Puerta de enlace de VPN y ExpressRoute coexisten**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU básica** | 100 Mbps | 10 | 500 Mbps | No |
| **SKU estándar** | 100 Mbps | 10 | 1000 Mbps | Sí |
| **SKU de alto rendimiento** | 200 Mbps | 30 | 2000 Mbps | Sí |


**Nota:** el rendimiento de la VPN es una estimación aproximada basada en las medidas entre redes virtuales en la misma región de Azure. No es una garantía de lo que puede obtener para conexiones entre entornos a través de Internet, sino que debe usarse como una medida máxima posible.

<!---HONumber=AcomDC_0224_2016-->