<properties
   pageTitle="Serialización de servicio de confianza | Microsoft Azure"
   description="Documentación conceptual para la serialización del Reliable Services de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Serialización en Reliable Services
El administrador de estado fiable puede contener varios objetos de confianza. Algunos de estos objetos de confianza pueden ser estructuras de datos genéricos como diccionario y la cola fiables listos para usar. Dado que son estructuras de datos genéricos de confianza, los objetos genéricos que contienen deben serializarse para que se puedan replicar y mantener en el disco.

El administrador de estado de confianza admite tres tipos de serializadores; * Los serializadores personalizados, * serializadores integrados para un número limitado de tipos, * DataContractSerilizer

Cuando se necesita serializar un objeto de confianza, consulta al administrador de estado de confianza un serializador del tipo dado. El administrador de estado de confianza comprobará primero si hay un serializador personalizado registrado para el tipo de entrada. De lo contrario, comprobará si uno de los serializadores integrados puede serializar el tipo. El administrador de estado de confianza tiene serializadores integrados para los siguientes tipos: guid, bool, byte, sbyte, char, decimal, double, float, int, uint, long, ulong, short, ushort y string. De lo contrario, devolverá DataContractSerializer.

Este artículo se centra en cuándo y cómo usar la serialización personalizada.

## Cuándo usar la serialización personalizada
Hay dos razones comunes para usar la serialización personalizada de cifrado de * Rendimiento *

Para los tipos que no están cubiertos por los tipos integrados, se pueden lograr considerables mejoras de rendimiento mediante serializadores personalizados en lugar de DataContractSerializer. Una razón es que los serializadores personalizados no necesitan serializar información de tipos. Service Fabric garantiza que a un serializador de estado para un tipo determinado solo se le conceda ese tipo para serializar y deserializar.

Los datos serializados generados por los serializadores se replican y se conserva en el disco como están. Para los datos que son confidenciales, puede resultar aconsejable asegurarse de que los bits del cable y el disco están cifrados.

## Cómo usar la serialización personalizada
Para usar un serializador personalizado para un tipo determinado, necesitamos * Crear un serializador de estado personalizado * Registrar el serializador de estado personalizado en un servicio fiable

### Cómo implementar un serializador personalizado
Los serializadores personalizados deben implementar la interfaz IStateSerializer<T>. Los dos métodos principales de esta interfaz son * T Read(BinaryReader binaryReader); * void Write (T value, BinaryWriter binaryWriter);

En primer lugar lo usa ReliableObject para leer el objeto serializado de una secuencia mediante un BinaryReader. En segundo lugar, se usa para la operación inversa: para escribir el objeto en una secuencia usando un escritor binario.

A continuación se muestra una clase personalizada de ejemplo y un serializador para esta.

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]En el ejemplo anterior, la implementación de las sobrecargas de lectura y escritura simplemente llaman a sus sobrecargas homólogas. Esto se debe a que los dos métodos siguientes se van a usar para una característica que todavía no está disponible.

### Cómo registrar un serializador de cliente
Para registrar un serializador personalizado, primero es necesario disponer de un método que pueda registrar todos los serializadores personalizados. Este método no debe tomar ningún argumento y ser capaz de devolver una tarea. En este método, IReliableStateManager.TryAddStateSerializer<T> se debe usar para registrar todos los serializadores personalizados para el servicio fiable.

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

El siguiente paso es registrar el método anterior como un delegado que será llamado por el Administrador de estado fiable en el momento en que se deben registrar todos los serializadores de estado personalizados. Solo se llama a este método al comienzo de la réplica de un servicio de confianza antes de iniciar la recuperación local, ya que podrían requerirse serializadores para leer los datos serializados del disco. Una vez registrado el serializador, el tipo correspondiente de todos los objetos de confianza usará este serializador para serializar y deserializar los objetos.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### Control de versiones
Service Fabric espera que los serializadores sean infinitamente compatibles en todos los sentidos. Para los tipos que usan los serializadores integrados, Service Fabric garantiza compatibilidad en todos los sentidos. Para los tipos que usan DataContractSerializer o el serializador personalizado, se pide al usuario que nunca realice un cambio importante. Para obtener información acerca del control de versiones de DataContract, consulte [Control de versiones del contratos de datos](https://msdn.microsoft.com/library/ms731138.aspx). Si se requiere un cambio importante, es necesario mover el estado de una instancia de servicio con la versión de datos antigua a un servicio con la nueva versión de datos en el nivel de aplicación.

### Cuando se usa un serializador
 * Escribir operaciones en objetos de confianza provocará que se puedan serializar, replicar y almacenar en un registro.
 * Una vez registradas suficientes operaciones, los datos más recientes de la memoria se serializan y marcan en el disco.
 * Para volver a crear una réplica, los archivos marcados en el disco y los datos recientes del registro se envían directamente byte por byte (es decir, los datos no se vuelven a serializar) desde una réplica principal. Estos bytes se deserializan en objetos de C# en la réplica secundaria.
 * Durante la recuperación, los archivos de punto de control y el registro de datos recientes se deserializan.
 * Durante la copia de seguridad, los archivos de punto de comprobación y los datos del registro recientes se copian byte a byte.
 * Durante la restauración, los archivos de punto de comprobación y los datos de registro de los que se hizo una copia de seguridad previamente se vuelven a copiar en su lugar y se deserializarán.

## Pasos siguientes
 * [Uso avanzado del modelo de programación de servicios fiables](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->