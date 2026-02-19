# 📡 Azure Event Hubs - Guía Completa

## 📖 Definición Detallada

Azure Event Hubs es una **plataforma de streaming de big data y servicio de ingesta de eventos** completamente administrada, capaz de recibir y procesar millones de eventos por segundo. Actúa como un "front door" para pipelines de datos en tiempo real, ofreciendo streaming de eventos con baja latencia, alta disponibilidad y escalabilidad masiva compatible con Apache Kafka.

**Conceptos fundamentales:**

**1. Event Hub (Instancia):**
- Stream de eventos individual
- Similar a un topic en Kafka
- Múltiples consumidores pueden leer del mismo hub
- Retención configurable: 1-90 días (7 días default)

**2. Partitions (Particiones):**
- **Unidad de paralelismo:** 2-32 particiones por Event Hub
- **Ordering:** Garantizado dentro de la partición, no cross-partition
- **Partition Key:** Determina a qué partición va el evento
- **Throughput:** Más particiones = más throughput paralelo
- **Consumer Group:** Cada consumidor lee una o más particiones

**3. Consumer Groups:**
- Vista independiente del stream de eventos
- Permite múltiples aplicaciones leer el mismo hub sin interferir
- Cada consumer group mantiene su propio offset
- Máximo 20 consumer groups por Event Hub (default 5)

**4. Throughput Units (TUs) / Processing Units (PUs):**
- **Standard tier:** Throughput Units (TUs)
  - 1 TU = 1 MB/s ingress, 2 MB/s egress
  - Hasta 40 TUs
- **Premium tier:** Processing Units (PUs)
  - 1 PU = ~1 TU pero con CPU/memoria dedicada
  - Hasta 16 PUs
- **Dedicated tier:** Capacity Units (CUs)
  - 1 CU = ~100 TUs

**5. Capture (Event Hubs Capture):**
- Captura automática de eventos a Azure Storage o Data Lake
- Formato Avro
- Configurable por tiempo (5-15 min) o tamaño (10-500 MB)
- Sin código, habilitar con un click

**6. Kafka Compatibility:**
- Wire protocol compatible con Apache Kafka
- Clientes Kafka nativos funcionan sin cambios
- Kafka Connect, Kafka Streams compatibles
- No necesitas gestionar clústeres Kafka

### 🎯 Importancia

**Por qué Event Hubs es crítico en Big Data:**

1. **Ingesta masiva:** Millones de eventos/segundo
2. **Streaming en tiempo real:** Latencia <100ms
3. **Durabilidad:** Eventos persisten 1-90 días (replay)
4. **Escalabilidad elástica:** De 1 a millones msg/sec sin downtime
5. **Multi-consumer:** Múltiples apps leen el mismo stream
6. **Kafka drop-in:** Migrar de Kafka sin cambiar código
7. **Capture automático:** Archive a Data Lake sin código
8. **Integración Azure:** Synapse, Stream Analytics, Databricks
9. **Geo-replication:** Disaster recovery en otra región
10. **Economía:** Más barato que gestionar Kafka clusters

### ✨ Características Principales

**1. Tiers Comparison:**

| Feature | Basic | Standard | Premium | Dedicated |
|---------|-------|----------|---------|-----------|
| **Precio/mes** | ~$11 + ingress | ~$25 + ingress | ~$680 + ingress | ~$8,000 |
| **Max throughput** | 1 MB/s | 20 MB/s (40 TUs) | 16 MB/s (16 PUs) | ~100+ MB/s |
| **Max partitions** | 32 | 32 | 100 | 1024 |
| **Retention** | 1 día | 7 días | 90 días | 90 días |
| **Capture** | ❌ | ✅ | ✅ | ✅ |
| **Consumer groups** | 1 | 20 | 100 | Ilimitado |
| **Kafka** | ❌ | ✅ | ✅ | ✅ |
| **VNet** | ❌ | ❌ | ✅ | ✅ |
| **Auto-inflate** | ❌ | ✅ | ✅ | N/A |
| **Geo-DR** | ❌ | ✅ | ✅ | ✅ |
| **Zone redundancy** | ❌ | ❌ | ✅ | ✅ |
| **IP filtering** | ❌ | ❌ | ✅ | ✅ |

**2. Partitioning Strategy:**

```csharp
// Sin partition key (round-robin)
await producer.SendAsync(new EventData(Encoding.UTF8.GetBytes("data")));

// Con partition key (mismo key → misma partición)
var eventData = new EventData(Encoding.UTF8.GetBytes("order data"))
{
    PartitionKey = "user-123"  // Garantiza orden para este usuario
};
await producer.SendAsync(eventData);

// Partition específica (rara vez necesario)
var sendOptions = new SendEventOptions { PartitionId = "0" };
await producer.SendAsync(eventData, sendOptions);
```

**3. Consumer Groups:**

```csharp
// Consumer Group 1: Real-time analytics
var consumer1 = new EventHubConsumerClient(
    consumerGroup: "analytics",
    connectionString,
    eventHubName
);

// Consumer Group 2: Audit logging (mismo stream)
var consumer2 = new EventHubConsumerClient(
    consumerGroup: "audit",
    connectionString,
    eventHubName
);

// Ambos leen todos los eventos independientemente
```

**4. Checkpointing (Offset Management):**

```csharp
// Processor automáticamente checkpoints en Blob Storage
var processor = new EventProcessorClient(
    blobContainerClient,
    consumerGroup,
    connectionString,
    eventHubName
);

processor.ProcessEventAsync += async args =>
{
    // Procesar evento
    await ProcessEventAsync(args.Data);
    
    // Checkpoint automático cada 10 eventos (configurable)
    await args.UpdateCheckpointAsync();
};

await processor.StartProcessingAsync();
```

**5. Event Hubs Capture:**

```yaml
Configuration:
  Enabled: true
  Destination: Azure Storage / Data Lake
  Time Window: 5-15 minutes
  Size Window: 10-500 MB
  Format: Avro
  Path Template: {Namespace}/{EventHub}/{ConsumerGroup}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}

Output:
  - Automatic archival sin código
  - Query con Synapse, Databricks, HDInsight
  - Cold storage para compliance
  - Replay histórico

Costo:
  - Gratis (solo pagas storage)
```

**6. Scaling Patterns:**

```yaml
# Auto-inflate (Standard tier)
- Habilitar auto-inflate
- Max TUs: 40
- Escala automáticamente cuando ingress > 80%
- Escala hacia abajo manualmente

# Premium tier
- PUs dedicados (CPU/RAM aislado)
- Sin noisy neighbors
- Scale up/down sin downtime

# Dedicated tier
- Cluster dedicado (1-8 CUs)
- Para ultra-high throughput (TB/día)
- Bring Your Own Key (BYOK)
```

**7. Integration Patterns:**

```mermaid
IoT Devices → Event Hubs → Stream Analytics → Cosmos DB
                         → Azure Functions → Service Bus
                         → Databricks → Data Lake
                         → Synapse Analytics → Power BI
```

### ✅ Ventajas

1. **Throughput masivo:** Millones de eventos/segundo
2. **Kafka compatible:** Drop-in replacement sin cambios de código
3. **Replay:** Eventos persisten hasta 90 días
4. **Multi-consumer:** Múltiples apps leen sin interferir
5. **Capture automático:** Archive a Data Lake sin código
6. **Managed service:** Sin gestión de Kafka clusters
7. **Escalabilidad elástica:** Auto-inflate de 1 a 40 TUs
8. **Geo-replication:** Disaster recovery automático
9. **Integración nativa:** Stream Analytics, Databricks, Synapse
10. **Costo-efectivo:** Más barato que self-managed Kafka
11. **Ordering:** Garantizado dentro de partición
12. **Baja latencia:** <100ms end-to-end
13. **Zone redundancy:** Alta disponibilidad (Premium)
14. **Schema Registry:** (Premium) valida eventos
15. **VNet integration:** (Premium) acceso privado

### ❌ Desventajas

1. **No es message queue:** No es request-reply como Service Bus
2. **Sin filtros:** No hay routing como Service Bus Topics
3. **Sin transacciones:** No hay ACID cross-partition
4. **Costo de Premium alto:** ~$680/mes mínimo
5. **Complejidad de consumer:** Debes gestionar offsets manualmente
6. **Límite de retención:** Max 90 días (vs Kafka ilimitado)
7. **No soporta compaction:** Kafka log compaction no disponible
8. **Ordering solo intra-partition:** Cross-partition no garantizado
9. **Consumer group limit:** 20 en Standard (vs Kafka ilimitado)
10. **Lock-in parcial:** Kafka API pero features propietarias
11. **Cold start:** Procesamiento histórico puede ser lento
12. **No es real-time bidirectional:** No reemplaza WebSockets
13. **Debugging complejo:** Eventos en stream difíciles de rastrear
14. **Tamaño de evento:** 1 MB max (vs Kafka 100 MB con config)
15. **No elimina eventos:** No puedes borrar eventos específicos

### 🎯 Cuándo Aplicarlo

**✅ USAR Azure Event Hubs cuando:**

1. **High-throughput ingestion:** >1,000 eventos/segundo
2. **Streaming analytics:** Análisis en tiempo real de logs, telemetría
3. **IoT telemetry:** Millones de dispositivos enviando datos
4. **Event sourcing:** Almacenar eventos como log inmutable
5. **Change data capture:** Stream de cambios de DB
6. **Multi-consumer scenarios:** Múltiples apps leen mismo stream
7. **Replay required:** Necesitas reprocessar eventos históricos
8. **Kafka migration:** Migrar de Kafka sin cambiar código
9. **Data lake ingestion:** Capture automático a ADLS
10. **Real-time dashboards:** Métricas en vivo
11. **Log aggregation:** Centralizar logs de múltiples servicios
12. **Click-stream analysis:** Tracking de usuario en tiempo real
13. **Fraud detection:** Análisis de transacciones en tiempo real
14. **Gaming telemetry:** Eventos de juego a escala masiva

**Ejemplos específicos:**
- **IoT:** 100k sensores enviando telemetría cada segundo
- **E-commerce:** Click-stream de millones de usuarios
- **Gaming:** Eventos de 10M+ jugadores concurrentes
- **Financial:** Transacciones de trading en tiempo real
- **Healthcare:** Streaming de dispositivos médicos
- **Telco:** CDR (Call Detail Records) processing
- **Social media:** Feed updates, likes, comments en tiempo real

**❌ NO USAR Azure Event Hubs cuando:**

1. **Low volume:** <100 eventos/segundo → Service Bus Queue más barato
2. **Request-reply required:** Necesitas respuestas síncronas → HTTP API
3. **Transaccional messaging:** ACID cross-entity → Service Bus
4. **Routing complejo:** Filtros SQL → Service Bus Topics
5. **Small messages con TTL corto:** <1 hora → Redis Pub/Sub
6. **Bidirectional real-time:** WebSockets → SignalR Service
7. **Dead-letter handling:** Necesitas DLQ → Service Bus
8. **Order guarantee cross-partition:** Event Hubs no lo garantiza
9. **Message deletion:** No puedes eliminar eventos específicos
10. **Budget muy limitado:** Storage Queue $0.05/mes vs Event Hubs $11/mes
11. **No necesitas replay:** Service Bus es más simple
12. **Transient events:** <1 min retención → In-memory queue

### 📋 Situaciones Específicas

**Escenario 1: IoT telemetry (100k dispositivos, 1 msg/min)**
- ✅ Event Hubs: 100k msg/min = ~1,700 msg/sec (fácil)
- ✅ Capture a Data Lake para análisis histórico

**Escenario 2: Order processing (100 orders/día)**
- ❌ Event Hubs: Overkill y caro
- ✅ Service Bus Queue: Más apropiado y barato

**Escenario 3: Real-time fraud detection**
- ✅ Event Hubs → Stream Analytics → Cosmos DB + alerts
- ✅ Múltiples consumer groups: analytics, ML, audit

**Escenario 4: Click-stream (1M users, 10 clicks/sesión)**
- ✅ Event Hubs: 10M clicks/día
- ✅ Capture a Data Lake
- ✅ Stream Analytics para dashboards en vivo

**Escenario 5: Migración de Kafka on-premises**
- ✅ Event Hubs: Kafka API compatible
- ✅ Sin cambios de código, solo connection string

**Escenario 6: Simple notification system**
- ❌ Event Hubs: Demasiado complejo
- ✅ Service Bus Topic con subscriptions

**Escenario 7: Gaming leaderboard updates**
- ✅ Event Hubs: Millones de eventos de juego
- ✅ Consumer: Actualiza leaderboard en Redis

**Escenario 8: Audit log con retention 5 años**
- ⚠️ Event Hubs: Max 90 días
- ✅ Event Hubs Capture → Data Lake (retention ilimitado)

### 🔧 Políticas y Mejores Prácticas

#### 1. Partition Strategy

```csharp
// ✅ BIEN: Partition key para orden dentro de entidad
var eventData = new EventData(orderJson)
{
    PartitionKey = $"user-{userId}"  // Todos los eventos de user ordenados
};

// ❌ MAL: Partition key con baja cardinalidad (hot partition)
var eventData = new EventData(orderJson)
{
    PartitionKey = "orders"  // Todos van a misma partition
};

// ✅ BIEN: Sin partition key si no necesitas orden
await producer.SendAsync(new EventData(data));  // Round-robin

// ✅ Calcular número de particiones
Partitions = Throughput (MB/s) / 1 MB/s (por partition)
Example: 10 MB/s → 10 partitions mínimo
         20 MB/s → 20 partitions mínimo
```

#### 2. Batch Processing

```csharp
// ✅ Batch sending (reduce costo y latencia)
var batch = await producer.CreateBatchAsync();

foreach (var order in orders)
{
    var eventData = new EventData(Encoding.UTF8.GetBytes(JsonSerializer.Serialize(order)));
    
    if (!batch.TryAdd(eventData))
    {
        // Batch lleno, enviar
        await producer.SendAsync(batch);
        
        batch = await producer.CreateBatchAsync();
        batch.TryAdd(eventData);
    }
}

// Enviar batch final
if (batch.Count > 0)
{
    await producer.SendAsync(batch);
}

// ✅ Batch receiving
await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync())
{
    var events = new List<EventData>();
    var timeout = DateTime.UtcNow.AddSeconds(5);
    
    do
    {
        events.Add(partitionEvent.Data);
    }
    while (DateTime.UtcNow < timeout && events.Count < 100);
    
    // Procesar batch
    await ProcessBatchAsync(events);
}
```

#### 3. Error Handling y Checkpointing

```csharp
processor.ProcessEventAsync += async args =>
{
    try
    {
        await ProcessEventAsync(args.Data);
        
        // Checkpoint cada 10 eventos (balance performance/durabilidad)
        if (args.Data.SequenceNumber % 10 == 0)
        {
            await args.UpdateCheckpointAsync();
        }
    }
    catch (TransientException ex)
    {
        // Retry con exponential backoff
        await RetryAsync(() => ProcessEventAsync(args.Data));
        await args.UpdateCheckpointAsync();
    }
    catch (Exception ex)
    {
        // Log y skip (no checkpoint)
        logger.LogError($"Failed to process: {ex.Message}");
        
        // Opcionalmente, enviar a DLQ (Service Bus)
        await deadLetterQueue.SendAsync(args.Data);
        
        // Checkpoint para no reprocessar infinitamente
        await args.UpdateCheckpointAsync();
    }
};

processor.ProcessErrorAsync += args =>
{
    logger.LogError($"Partition {args.PartitionId} error: {args.Exception}");
    return Task.CompletedTask;
};
```

#### 4. Performance Optimization

```csharp
// ✅ Connection pooling
private static readonly EventHubProducerClient producer = 
    new EventHubProducerClient(connectionString, eventHubName);

// ✅ Prefetch (reduce roundtrips)
var options = new EventProcessorClientOptions
{
    PrefetchCount = 300,  // Fetch 300 eventos a la vez
    MaximumWaitTime = TimeSpan.FromSeconds(10)
};

// ✅ Procesamiento paralelo por partition
processor.ProcessEventAsync += async args =>
{
    await Task.WhenAll(
        ProcessEventAsync(args.Data),
        UpdateMetricsAsync(args.Data),
        LogEventAsync(args.Data)
    );
    
    await args.UpdateCheckpointAsync();
};

// ✅ Compression (reduce network)
var compressedData = Compress(originalData);
await producer.SendAsync(new EventData(compressedData)
{
    ContentType = "application/gzip"
});
```

#### 5. Monitoring y Alerting

```yaml
Métricas críticas:
  - Incoming Messages: Events/sec ingresados
  - Outgoing Messages: Events/sec consumidos
  - Throttled Requests: Si >0, necesitas más TUs
  - Server Errors: >1% indica problemas
  - User Errors: >5% revisa código cliente
  - Capture Backlog: Retraso en captura
  - Consumer Lag: Diferencia offset consumer vs producer

Alertas:
  - Throttled Requests > 0 → Aumentar TUs
  - Consumer Lag > 1 millón → Consumer lento
  - Server Errors > 1% → Revisar logs
  - Capture Backlog > 1 hora → Revisar storage
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear Event Hubs Namespace

**Azure CLI:**
```bash
# Variables
RESOURCE_GROUP="rg-eventhubs-prod"
LOCATION="eastus"
NAMESPACE="eh-myapp-prod"
EVENT_HUB="telemetry"

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear namespace (Standard tier)
az eventhubs namespace create \
  --name $NAMESPACE \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --sku Standard \
  --capacity 1 \
  --enable-auto-inflate true \
  --maximum-throughput-units 20

# Crear Event Hub
az eventhubs eventhub create \
  --name $EVENT_HUB \
  --namespace-name $NAMESPACE \
  --resource-group $RESOURCE_GROUP \
  --partition-count 4 \
  --message-retention 7

# Consumer group
az eventhubs eventhub consumer-group create \
  --name analytics \
  --eventhub-name $EVENT_HUB \
  --namespace-name $NAMESPACE \
  --resource-group $RESOURCE_GROUP

# Obtener connection string
az eventhubs namespace authorization-rule keys list \
  --resource-group $RESOURCE_GROUP \
  --namespace-name $NAMESPACE \
  --name RootManageSharedAccessKey \
  --query primaryConnectionString -o tsv
```

**Premium tier con Capture:**
```bash
# Storage account para Capture
az storage account create \
  --name stehcapture \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --sku Standard_LRS

# Container
az storage container create \
  --name eventhubs-capture \
  --account-name stehcapture

# Premium namespace
az eventhubs namespace create \
  --name eh-premium-prod \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --sku Premium \
  --capacity 1 \
  --zone-redundant true

# Event Hub con Capture
az eventhubs eventhub create \
  --name telemetry \
  --namespace-name eh-premium-prod \
  --resource-group $RESOURCE_GROUP \
  --partition-count 8 \
  --message-retention 7 \
  --capture-enabled true \
  --capture-encoding Avro \
  --capture-interval 300 \
  --capture-size-limit 314572800 \
  --destination-name EventHubArchive.AzureBlockBlob \
  --storage-account /subscriptions/.../stehcapture \
  --blob-container eventhubs-capture \
  --archive-name-format "{Namespace}/{EventHub}/{ConsumerGroup}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}"
```

**Terraform:**
```hcl
resource "azurerm_eventhub_namespace" "eh" {
  name                = "eh-myapp-prod"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "Standard"
  capacity            = 1

  auto_inflate_enabled     = true
  maximum_throughput_units = 20

  tags = {
    Environment = "Production"
  }
}

resource "azurerm_eventhub" "telemetry" {
  name                = "telemetry"
  namespace_name      = azurerm_eventhub_namespace.eh.name
  resource_group_name = azurerm_resource_group.rg.name
  partition_count     = 4
  message_retention   = 7

  capture_description {
    enabled  = true
    encoding = "Avro"
    
    interval_in_seconds = 300
    size_limit_in_bytes = 314572800
    
    destination {
      name                = "EventHubArchive.AzureBlockBlob"
      archive_name_format = "{Namespace}/{EventHub}/{ConsumerGroup}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}"
      blob_container_name = azurerm_storage_container.capture.name
      storage_account_id  = azurerm_storage_account.capture.id
    }
  }
}

resource "azurerm_eventhub_consumer_group" "analytics" {
  name                = "analytics"
  namespace_name      = azurerm_eventhub_namespace.eh.name
  eventhub_name       = azurerm_eventhub.telemetry.name
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_eventhub_consumer_group" "audit" {
  name                = "audit"
  namespace_name      = azurerm_eventhub_namespace.eh.name
  eventhub_name       = azurerm_eventhub.telemetry.name
  resource_group_name = azurerm_resource_group.rg.name
}
```

### 💻 Ejemplo Completo - C# (.NET)

**Producer:**
```csharp
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Producer;

var connectionString = Environment.GetEnvironmentVariable("EventHubConnection");
var eventHubName = "telemetry";

await using var producer = new EventHubProducerClient(connectionString, eventHubName);

// Send single event
var eventData = new EventData(Encoding.UTF8.GetBytes(JsonSerializer.Serialize(new
{
    DeviceId = "sensor-001",
    Temperature = 25.5,
    Timestamp = DateTime.UtcNow
})))
{
    PartitionKey = "sensor-001",  // Orden garantizado por dispositivo
    ContentType = "application/json"
};

eventData.Properties.Add("DeviceType", "Temperature");
eventData.Properties.Add("Location", "Building-A");

await producer.SendAsync(new[] { eventData });

// Send batch
var batch = await producer.CreateBatchAsync();
for (int i = 0; i < 1000; i++)
{
    var telemetry = new EventData(Encoding.UTF8.GetBytes($"Data {i}"));
    
    if (!batch.TryAdd(telemetry))
    {
        await producer.SendAsync(batch);
        batch = await producer.CreateBatchAsync();
        batch.TryAdd(telemetry);
    }
}

if (batch.Count > 0)
{
    await producer.SendAsync(batch);
}

Console.WriteLine($"Sent {batch.Count} events");
```

**Consumer with EventProcessorClient:**
```csharp
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Consumer;
using Azure.Messaging.EventHubs.Processor;
using Azure.Storage.Blobs;

var blobStorageConnectionString = Environment.GetEnvironmentVariable("StorageConnection");
var blobContainerName = "checkpoints";
var consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

var storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);
await storageClient.CreateIfNotExistsAsync();

var processor = new EventProcessorClient(
    storageClient,
    consumerGroup,
    connectionString,
    eventHubName,
    new EventProcessorClientOptions
    {
        PrefetchCount = 300,
        MaximumWaitTime = TimeSpan.FromSeconds(30)
    }
);

processor.ProcessEventAsync += async args =>
{
    try
    {
        var body = Encoding.UTF8.GetString(args.Data.Body.ToArray());
        var deviceType = args.Data.Properties["DeviceType"];
        
        Console.WriteLine($"Partition: {args.Partition.PartitionId}");
        Console.WriteLine($"Offset: {args.Data.Offset}");
        Console.WriteLine($"Sequence: {args.Data.SequenceNumber}");
        Console.WriteLine($"Device: {deviceType}");
        Console.WriteLine($"Data: {body}");
        
        // Process event
        await ProcessTelemetryAsync(body);
        
        // Checkpoint every 10 events
        if (args.Data.SequenceNumber % 10 == 0)
        {
            await args.UpdateCheckpointAsync(args.CancellationToken);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error processing: {ex.Message}");
    }
};

processor.ProcessErrorAsync += args =>
{
    Console.WriteLine($"Error on partition {args.PartitionId}: {args.Exception.Message}");
    return Task.CompletedTask;
};

await processor.StartProcessingAsync();
Console.WriteLine("Processing started. Press any key to stop.");
Console.ReadKey();

await processor.StopProcessingAsync();
```

### 💻 Ejemplo con Kafka API (Java)

**Producer:**
```java
// Kafka producer funciona sin cambios
Properties props = new Properties();
props.put("bootstrap.servers", "eh-myapp-prod.servicebus.windows.net:9093");
props.put("security.protocol", "SASL_SSL");
props.put("sasl.mechanism", "PLAIN");
props.put("sasl.jaas.config", 
    "org.apache.kafka.common.security.plain.PlainLoginModule required " +
    "username=\"$ConnectionString\" " +
    "password=\"Endpoint=sb://eh-myapp-prod.servicebus.windows.net/;...\";");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

KafkaProducer<String, String> producer = new KafkaProducer<>(props);

ProducerRecord<String, String> record = new ProducerRecord<>(
    "telemetry",  // Event Hub name
    "sensor-001",  // Key (partition key)
    "{\"temperature\": 25.5}"  // Value
);

producer.send(record, (metadata, exception) -> {
    if (exception != null) {
        System.err.println("Error: " + exception.getMessage());
    } else {
        System.out.println("Sent to partition " + metadata.partition() + 
                           " offset " + metadata.offset());
    }
});

producer.close();
```

**Consumer:**
```java
Properties props = new Properties();
props.put("bootstrap.servers", "eh-myapp-prod.servicebus.windows.net:9093");
props.put("security.protocol", "SASL_SSL");
props.put("sasl.mechanism", "PLAIN");
props.put("sasl.jaas.config", "...");
props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
props.put("group.id", "analytics");  // Consumer group
props.put("auto.offset.reset", "earliest");

KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
consumer.subscribe(Collections.singletonList("telemetry"));

while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
    for (ConsumerRecord<String, String> record : records) {
        System.out.printf("Partition: %d, Offset: %d, Key: %s, Value: %s%n",
            record.partition(), record.offset(), record.key(), record.value());
        
        // Process record
        processTelemetry(record.value());
    }
    consumer.commitSync();
}
```

### 📊 Comparación: Event Hubs vs Alternativas

| Feature | Event Hubs | Service Bus | Storage Queue | Kafka (self-hosted) |
|---------|------------|-------------|---------------|---------------------|
| **Throughput** | Millones/sec | Miles/sec | Cientos/sec | Millones/sec |
| **Retención** | 1-90 días | Hasta completar | 7 días | Ilimitado |
| **Replay** | ✅ | ❌ | ❌ | ✅ |
| **Ordering** | Intra-partition | FIFO con sessions | ❌ | Intra-partition |
| **Multi-consumer** | ✅ Consumer groups | ❌ Competing | ❌ Competing | ✅ Consumer groups |
| **Transacciones** | ❌ | ✅ | ❌ | ✅ |
| **Precio/mes** | ~$25+ | ~$10+ | ~$0.05 | $500+ (VMs) |
| **Managed** | ✅ | ✅ | ✅ | ❌ |
| **Kafka API** | ✅ | ❌ | ❌ | ✅ |
| **Capture** | ✅ Built-in | ❌ | ❌ | Kafka Connect |
| **Use case** | Streaming, IoT | Messaging | Simple queue | Streaming, control total |

---

Esta es la guía completa de Azure Event Hubs. Dado que nos estamos acercando al límite de tokens, ¿deseas que continúe con más servicios o prefieres un resumen de lo documentado hasta ahora? 

Tenemos **8 de 18 servicios** completados (44%). 🚀