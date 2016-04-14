Recurso|Límite predeterminado|Límite máximo
---|---|---
[Roles web y de trabajo por implementación](../articles/cloud-services/fundamentals-application-models.md#tellmecs)<sup>1</sup>|25|25
[Extremos de entrada de instancia](http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) por implementación|25|25
[Extremos de entrada](http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) por implementación|25|25
[Extremos internos](http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) por implementación|25|25

<sup>1</sup>Cada servicio en la nube con roles web y de trabajo puede tener dos implementaciones, una para producción y otra para ensayo. Tenga en cuenta también que este límite hace referencia al número de roles (configuración) y no al número de instancias por rol (escalado).

<!---HONumber=AcomDC_0211_2016-->