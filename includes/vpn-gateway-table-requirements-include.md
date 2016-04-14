La tabla siguiente enumera los requisitos para las puertas de enlace VPN basadas en enrutamientos y directivas. Esta tabla se aplica a los modelos de implementación del Administrador de recursos y clásico. Para el modelo clásico, las puertas de enlace de VPN basadas en directivas son las mismas que las puertas de enlace estáticas y las basadas en enrutamientos son las mismas que las dinámicas.


| | **Puerta de enlace de VPN básica basada en directivas** | **Puerta de enlace de VPN básica basada en enrutamientos** | **Puerta de enlace de VPN estándar basada en enrutamientos** | **Puerta de enlace de VPN de alto rendimiento basada en enrutamientos** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
| **Conectividad de sitio a sitio (S2S)** | Configuración de VPN basada en directivas | Configuración de VPN basada en enrutamiento | Configuración de VPN basada en enrutamiento | Configuración de VPN basada en enrutamiento |
| **Conectividad de punto a sitio (P2S**) | No compatible | Compatible (puede coexistir con S2S) | Compatible (puede coexistir con S2S) | Compatible (puede coexistir con S2S) |
| **Método de autenticación** | Clave precompartida | Clave precompartida para la conectividad de S2S, Certificados para la conectividad de P2S | Clave precompartida para la conectividad de S2S, Certificados para la conectividad de P2S | Clave precompartida para la conectividad de S2S, Certificados para la conectividad de P2S |
| **Número máximo de conexiones S2S** | 1 | 10 | 10 | 30 |
| **Número máximo de conexiones P2S** | No compatible | 128 | 128 | 128 |
|**Compatibilidad con enrutamiento activo (BGP)** | No compatible | No compatible | No compatible | No compatible |
 

<!---HONumber=AcomDC_0224_2016-->