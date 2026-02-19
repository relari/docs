# 🚌 Azure Service Bus - Guía Completa

## 📖 Definición Detallada

Azure Service Bus es un **servicio de mensajería empresarial completamente administrado** que proporciona comunicación asíncrona confiable entre aplicaciones y servicios distribuidos mediante colas (queues) y temas (topics) con suscripciones. Garantiza entrega de mensajes con soporte transaccional, ordenamiento FIFO, deduplicación y capacidades avanzadas de routing.

**Conceptos fundamentales:**

**1. Namespace:**
- Contenedor de todas las entidades de mensajería (queues, topics)
- Proporciona un endpoint único: `myapp.servicebus.windows.net`
- Unidad de facturación y configuración
- Tiers: Basic, Standard, Premium

**2. Queues (Colas):**
- **Point-to-Point:** Un mensaje → un consumidor
- **FIFO:** First In, First Out (con sessions)
- **Competencia entre consumidores:** Múltiples workers consumen la misma cola
- **Dead-letter queue:** Mensajes que no pudieron procesarse
- **Max size:** Hasta 80 GB (Premium)
- **TTL:** Time-to-live configurable por mensaje o cola

**3. Topics y Subscriptions:**
- **Pub/Sub:** Un mensaje → múltiples suscriptores
- **Topic:** Canal de publicación
- **Subscription:** Filtro de mensajes (SQL, correlación)
- **Múltiples suscripciones:** Cada una recibe copia del mensaje
- **Filtros:** Routing basado en propiedades del mensaje

**4. Mensajes:**
- **Payload:** Hasta 256 KB (Standard) o 1 MB (Premium) por mensaje
- **Batch:** Múltiples mensajes en una transacción
- **Properties:** Metadata (ContentType, CorrelationId, SessionId, etc.)
- **Custom properties:** Key-value pairs para routing

**5. Características Avanzadas:**

**Sessions (Ordenamiento FIFO garantizado):**
- Agrupa mensajes relacionados con mismo SessionId
- Garantiza procesamiento ordenado dentro de la sesión
- Lock de sesión (solo un consumidor a la vez)
- State management por sesión

**Dead-Letter Queue (DLQ):**
- Cola automática para mensajes fallidos
- Razones: MaxDeliveryCount excedido, TTL expirado, filtro rechazado
- Permite análisis post-mortem de fallos
- Cada queue/subscription tiene su propia DLQ

**Transactions:**
- Operaciones atómicas (send, receive, complete)
- Send-via: Enviar a otra cola dentro de una transacción
- Cross-entity transactions en Premium

**Duplicate Detection:**
- Elimina mensajes duplicados basado en MessageId
- Ventana de detección configurable (1 min - 7 días)
- Útil para idempotencia

**Scheduled Messages:**
- Enviar mensaje para ser entregado en el futuro
- ScheduledEnqueueTimeUtc property
- Útil para reminders, delays

**Auto-forwarding:**
- Reenviar automáticamente mensajes entre queues/topics
- Chain queues para routing complejo
- No cuenta contra quota de operaciones

### 🎯 Importancia

**Por qué Service Bus es crítico en arquitecturas distribuidas:**

1. **Desacoplamiento:** Productores y consumidores no necesitan estar activos simultáneamente
2. **Escalabilidad:** Múltiples consumidores procesan mensajes en paralelo
3. **Confiabilidad:** Garantías de entrega (At-least-once, exactamente una vez con sessions)
4. **Resiliencia:** Mensajes persisten si consumidor falla
5. **Load leveling:** Absorbe picos de tráfico sin abrumar consumidores
6. **Orden garantizado:** Sessions aseguran FIFO estricto
7. **Transacciones:** Operaciones atómicas entre múltiples entidades
8. **Pub/Sub avanzado:** Routing inteligente con filtros SQL
9. **Enterprise-grade:** SLA 99.9%, geo-replication (Premium)
10. **Integration:** Triggers nativos en Azure Functions, Logic Apps

### ✨ Características Principales

**1. Tiers Comparison:**

| Feature | Basic | Standard | Premium |
|---------|-------|----------|---------|
| **Precio/mes** | ~$10 | ~$10 + ops | ~$670+ |
| **Max message size** | 256 KB | 256 KB | 1 MB |
| **Max queue/topic size** | 1 GB | 5 GB | 80 GB |
| **Topics** | ❌ | ✅ | ✅ |
| **Transactions** | ❌ | ✅ | ✅ |
| **Duplicate detection** | ❌ | ✅ | ✅ |
| **Sessions** | ❌ | ✅ | ✅ |
| **Partitioning** | ❌ | ✅ | ❌ (no necesita) |
| **Throughput** | Compartido | Compartido | Dedicado |
| **VNet integration** | ❌ | ❌ | ✅ |
| **Geo-disaster recovery** | ❌ | ❌ | ✅ |
| **IP filtering** | ❌ | ❌ | ✅ |
| **SLA** | 99.9% | 99.9% | 99.95% |

**2. Message Properties:**

```csharp
// System Properties (auto-generated)
message.MessageId          // Único
message.SequenceNumber     // Orden de enqueue
message.EnqueuedTimeUtc    // Timestamp
message.DeliveryCount      // Número de intentos
message.LockedUntilUtc     // Lock expiration
message.SessionId          // Para FIFO

// Application Properties (custom)
message.ApplicationProperties["OrderType"] = "Express";
message.ApplicationProperties["Priority"] = 1;
message.ApplicationProperties["Region"] = "US-East";

// Message Metadata
message.ContentType = "application/json";
message.CorrelationId = correlationId;
message.To = "order-processor";
message.ReplyTo = "responses-queue";
message.TimeToLive = TimeSpan.FromHours(24);
```

**3. Subscription Filters:**

**SQL Filter:**
```csharp
// Crear subscription con filtro SQL
var ruleDescription = new RuleDescription
{
    Filter = new SqlFilter("Priority = 1 AND Region = 'US-East'"),
    Name = "HighPriorityUSEast"
};

await administrationClient.CreateRuleAsync(
    topicName, 
    "high-priority-subscription", 
    ruleDescription
);
```

**Correlation Filter (más eficiente):**
```csharp
var filter = new CorrelationRuleFilter
{
    CorrelationId = "order-123",
    Subject = "Order",
    ApplicationProperties = 
    {
        { "Priority", 1 }
    }
};
```

**Action (modificar mensaje):**
```csharp
var rule = new RuleDescription
{
    Filter = new SqlFilter("Priority = 1"),
    Action = new SqlRuleAction("SET Region = 'ProcessedInEast'")
};
```

**4. Sessions (FIFO garantizado):**

```csharp
// Send with SessionId
var message = new ServiceBusMessage("Order data")
{
    SessionId = "user-123"  // Todos los mensajes de este user en orden
};
await sender.SendMessageAsync(message);

// Receive with Session
await using ServiceBusSessionReceiver sessionReceiver = 
    await client.AcceptNextSessionAsync(queueName);

// Procesa mensajes de la sesión en orden FIFO
await foreach (ServiceBusReceivedMessage message in sessionReceiver.ReceiveMessagesAsync())
{
    // Garantizado en orden dentro de SessionId "user-123"
    await ProcessMessage(message);
    await sessionReceiver.CompleteMessageAsync(message);
}

// Session state (opcional)
await sessionReceiver.SetSessionStateAsync(new BinaryData("state"));
BinaryData state = await sessionReceiver.GetSessionStateAsync();
```

**5. Transactions:**

```csharp
using var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled);

// Recibir mensaje
ServiceBusReceivedMessage receivedMessage = await receiver.ReceiveMessageAsync();

// Procesar
var processedData = ProcessMessage(receivedMessage);

// Enviar resultado a otra cola (en misma transacción)
await sender.SendMessageAsync(new ServiceBusMessage(processedData));

// Completar mensaje original
await receiver.CompleteMessageAsync(receivedMessage);

ts.Complete();  // Commit transacción
```

**6. Dead-Letter Queue Handling:**

```csharp
// Mensaje va a DLQ si:
// - DeliveryCount > MaxDeliveryCount (default 10)
// - TTL expirado
// - Manually dead-lettered

// Manual dead-letter
await receiver.DeadLetterMessageAsync(message, 
    deadLetterReason: "Invalid format",
    deadLetterErrorDescription: "JSON parsing failed"
);

// Procesar DLQ
ServiceBusReceiver dlqReceiver = client.CreateReceiver(
    queueName,
    new ServiceBusReceiverOptions
    {
        SubQueue = SubQueue.DeadLetter
    }
);

await foreach (ServiceBusReceivedMessage dlqMessage in dlqReceiver.ReceiveMessagesAsync())
{
    // Analizar, loggear, alertar
    var reason = dlqMessage.DeadLetterReason;
    var description = dlqMessage.DeadLetterErrorDescription;
    
    // Opcionalmente, reenviar a cola original después de corregir
    await sender.SendMessageAsync(new ServiceBusMessage(dlqMessage.Body));
    await dlqReceiver.CompleteMessageAsync(dlqMessage);
}
```

**7. Scheduled Messages:**

```csharp
// Enviar mensaje para ser entregado en 1 hora
var message = new ServiceBusMessage("Delayed task");
long sequenceNumber = await sender.ScheduleMessageAsync(
    message,
    DateTimeOffset.UtcNow.AddHours(1)
);

// Cancelar mensaje programado
await sender.CancelScheduledMessageAsync(sequenceNumber);
```

**8. Batch Operations:**

```csharp
// Send batch (más eficiente)
var batch = await sender.CreateMessageBatchAsync();
for (int i = 0; i < 1000; i++)
{
    var message = new ServiceBusMessage($"Message {i}");
    if (!batch.TryAddMessage(message))
    {
        // Batch lleno, enviar y crear nuevo
        await sender.SendMessagesAsync(batch);
        batch = await sender.CreateMessageBatchAsync();
        batch.TryAddMessage(message);
    }
}
await sender.SendMessagesAsync(batch);  // Enviar último batch

// Receive batch
IReadOnlyList<ServiceBusReceivedMessage> messages = 
    await receiver.ReceiveMessagesAsync(maxMessages: 100);

foreach (var message in messages)
{
    await ProcessMessageAsync(message);
    await receiver.CompleteMessageAsync(message);
}
```

### ✅ Ventajas

1. **Garantías de entrega:** At-least-once, exactly-once con sessions
2. **FIFO estricto:** Con sessions, garantiza orden
3. **Transacciones:** Operaciones atómicas entre queues
4. **Duplicate detection:** Idempotencia automática
5. **Dead-letter queue:** Manejo robusto de fallos
6. **Pub/Sub avanzado:** Filtros SQL para routing inteligente
7. **Enterprise features:** VNet, geo-DR, IP filtering (Premium)
8. **SLA alto:** 99.9% (Standard), 99.95% (Premium)
9. **Escalabilidad:** Hasta 80 GB de mensajes (Premium)
10. **Integration:** Azure Functions, Logic Apps triggers nativos
11. **Seguridad:** RBAC, Managed Identity, SAS tokens
12. **Monitoring:** Azure Monitor, métricas detalladas
13. **Auto-forwarding:** Chain queues sin código
14. **Scheduled messages:** Delays sin infraestructura adicional
15. **Long polling:** Eficiente para consumers

### ❌ Desventajas

1. **Costo Premium alto:** ~$670/mes mínimo (vs Storage Queue $0.05/mes)
2. **Complejidad:** Más complejo que Storage Queue para casos simples
3. **Latencia:** ~10-50ms (vs Redis Pub/Sub <1ms)
4. **Tamaño de mensaje limitado:** 1 MB max (vs Event Hubs GB)
5. **No es streaming:** No para high-throughput continuous streams (usar Event Hubs)
6. **Throughput limitado:** ~2,000 msg/sec en Standard (vs Event Hubs 1M+/sec)
7. **Sin replay:** Mensajes se eliminan tras completarse (vs Kafka/Event Hubs)
8. **Lock-in de Azure:** Difícil migrar a otras clouds sin refactoring
9. **Basic tier limitado:** Sin topics, sessions, transacciones
10. **Overhead de management:** Namespace, queues, topics requieren configuración
11. **Cold start con sessions:** Primera sesión puede tardar
12. **Límites de partitioning:** Standard tiene límites vs Premium
13. **Sin message ordering cross-queue:** Solo dentro de queue/session
14. **Debugging complejo:** Mensajes en DLQ requieren análisis manual
15. **Costo de operaciones:** Standard cobra por operaciones (millones)

### 🎯 Cuándo Aplicarlo

**✅ USAR Azure Service Bus cuando:**

1. **Mensajería confiable:** Necesitas garantías de entrega At-least-once
2. **FIFO requerido:** Orden estricto de procesamiento (usar sessions)
3. **Transacciones:** Operaciones atómicas entre queues
4. **Pub/Sub con filtros:** Routing inteligente a múltiples consumidores
5. **Enterprise compliance:** VNet, geo-DR, SLA 99.95%
6. **Deduplicación:** Evitar procesamiento duplicado automáticamente
7. **Dead-letter handling:** Analizar mensajes fallidos
8. **Load leveling:** Absorber picos de tráfico sin abrumar backend
9. **Decoupling:** Productor y consumidor no activos simultáneamente
10. **Request-reply patterns:** Usar ReplyTo y CorrelationId
11. **Scheduled processing:** Delays y reminders
12. **Competing consumers:** Múltiples workers procesan misma cola
13. **Multi-step workflows:** Transactions entre múltiples pasos
14. **Integration patterns:** ESB (Enterprise Service Bus) patterns

**Ejemplos específicos:**
- **Order processing:** Queue para pedidos, garantiza no se pierden
- **Email sending:** Topic con múltiples subscriptions (email, SMS, push)
- **Payment processing:** Transacciones entre validación, cargo, factura
- **ETL pipelines:** Chain queues para multi-stage processing
- **Microservices communication:** Pub/Sub entre 10+ servicios
- **CQRS:** Commands en queue, events en topic
- **Saga pattern:** Transacciones distribuidas con compensación

**❌ NO USAR Azure Service Bus cuando:**

1. **Simple fire-and-forget:** Storage Queue es más barato ($0.05 vs $10/mes)
2. **High-throughput streaming:** >10,000 msg/sec → Usar Event Hubs
3. **Real-time bidirectional:** WebSockets → Usar SignalR Service
4. **Message replay required:** Kafka/Event Hubs permiten replay
5. **Ultra-low latency:** <5ms → Usar Redis Pub/Sub
6. **Mensajes > 1 MB:** Usar Event Hubs (hasta 1 GB con capture)
7. **Presupuesto muy limitado:** Storage Queue es 200x más barato
8. **No necesitas features avanzadas:** Topics, sessions, transactions
9. **Streaming analytics:** Azure Stream Analytics + Event Hubs
10. **IoT telemetría masiva:** IoT Hub + Event Hubs (millones msg/sec)
11. **Log aggregation:** Use Application Insights, Log Analytics
12. **File processing:** Blob Storage + Event Grid es más eficiente
13. **Sincronización en tiempo real:** Usa Cosmos DB Change Feed
14. **Multi-cloud portability crítica:** RabbitMQ, Kafka son más portables

### 📋 Situaciones Específicas

**Escenario 1: Notification system (email, SMS, push)**
- ✅ Service Bus Topic con 3 subscriptions (email-sub, sms-sub, push-sub)
- ✅ Filtros por preferencias de usuario
- ⭐ Ejemplo perfecto de Pub/Sub

**Escenario 2: Simple task queue (100 tasks/día)**
- ❌ Service Bus: Overkill y caro ($10/mes)
- ✅ Storage Queue: $0.05/mes
- ⚠️ Usa Service Bus solo si necesitas FIFO, transactions

**Escenario 3: Order processing con FIFO estricto**
- ✅ Service Bus Queue con Sessions (SessionId = userId)
- ✅ Garantiza órdenes de mismo usuario se procesan en orden

**Escenario 4: Payment processing (multi-step transaction)**
- ✅ Service Bus Transactions
- ✅ Steps: Validate → Charge → Invoice en transacción atómica

**Escenario 5: IoT telemetry (1 millón msg/segundo)**
- ❌ Service Bus: Max ~2,000 msg/sec en Standard
- ✅ Event Hubs: Diseñado para millones msg/sec

**Escenario 6: Microservices (20 servicios, event-driven)**
- ✅ Service Bus Topics + Subscriptions
- ✅ Cada servicio tiene subscription con filtros
- ⚠️ Si necesitas replay, considerar Event Hubs

**Escenario 7: ETL pipeline (multi-stage processing)**
- ✅ Service Bus auto-forwarding chain
- ✅ Queue1 → Queue2 → Queue3 automáticamente

**Escenario 8: Real-time chat**
- ❌ Service Bus: No diseñado para bidirectional real-time
- ✅ SignalR Service

### 🔧 Políticas y Mejores Prácticas

#### 1. Queue vs Topic Decision

```yaml
Usa Queue cuando:
  - Point-to-point (1 consumidor lógico)
  - Competing consumers (múltiples workers, 1 procesa)
  - FIFO estricto requerido
  - Ejemplos: Order processing, payment queue

Usa Topic cuando:
  - Pub/Sub (múltiples suscriptores independientes)
  - Routing basado en filtros
  - Fan-out (1 mensaje → N consumidores)
  - Ejemplos: Notifications, event broadcasting
```

#### 2. Sessions Best Practices

```csharp
// ✅ BIEN: SessionId para garantizar orden
var messages = new[]
{
    new ServiceBusMessage("Step 1") { SessionId = "order-123" },
    new ServiceBusMessage("Step 2") { SessionId = "order-123" },
    new ServiceBusMessage("Step 3") { SessionId = "order-123" }
};
// Garantizado: Se procesan en orden

// ❌ MAL: Sin SessionId si necesitas orden
var messages = new[]
{
    new ServiceBusMessage("Step 1"),  // Puede procesarse en cualquier orden
    new ServiceBusMessage("Step 2"),
    new ServiceBusMessage("Step 3")
};

// ✅ SessionId strategy
- Para FIFO por usuario: SessionId = userId
- Para FIFO por pedido: SessionId = orderId
- Para FIFO por tenant: SessionId = tenantId
```

#### 3. Error Handling y Retry

```csharp
// ✅ Configurar MaxDeliveryCount
await administrationClient.CreateQueueAsync(new CreateQueueOptions("myqueue")
{
    MaxDeliveryCount = 5  // Después de 5 intentos → DLQ
});

// ✅ Retry con exponential backoff
var options = new ServiceBusProcessorOptions
{
    MaxConcurrentCalls = 10,
    AutoCompleteMessages = false,  // Manual complete para control
    MaxAutoLockRenewalDuration = TimeSpan.FromMinutes(5)
};

processor.ProcessMessageAsync += async args =>
{
    try
    {
        await ProcessMessageAsync(args.Message);
        await args.CompleteMessageAsync(args.Message);
    }
    catch (TransientException ex)
    {
        // Abandona (reintenta automáticamente)
        await args.AbandonMessageAsync(args.Message);
    }
    catch (PermanentException ex)
    {
        // Dead-letter (no reintentar)
        await args.DeadLetterMessageAsync(
            args.Message,
            deadLetterReason: "PermanentFailure",
            deadLetterErrorDescription: ex.Message
        );
    }
};

// ✅ Monitor DLQ periódicamente
[FunctionName("MonitorDLQ")]
public async Task Run([TimerTrigger("0 */15 * * * *")] TimerInfo timer)
{
    var dlqReceiver = client.CreateReceiver(queueName, 
        new ServiceBusReceiverOptions { SubQueue = SubQueue.DeadLetter });
    
    var messages = await dlqReceiver.ReceiveMessagesAsync(100);
    
    if (messages.Any())
    {
        // Alertar al equipo
        await SendAlert($"{messages.Count} messages in DLQ");
    }
}
```

#### 4. Performance Optimization

```csharp
// ✅ Batch sending (reduce operaciones)
var batch = await sender.CreateMessageBatchAsync();
foreach (var item in items)
{
    if (!batch.TryAddMessage(new ServiceBusMessage(item)))
    {
        await sender.SendMessagesAsync(batch);
        batch = await sender.CreateMessageBatchAsync();
        batch.TryAddMessage(new ServiceBusMessage(item));
    }
}
await sender.SendMessagesAsync(batch);

// ✅ Prefetch (reduce roundtrips)
var options = new ServiceBusProcessorOptions
{
    PrefetchCount = 100  // Fetch 100 mensajes a la vez
};

// ✅ Connection pooling
private static readonly ServiceBusClient client = 
    new ServiceBusClient(connectionString);
// Reutiliza ServiceBusClient, no crees uno nuevo por mensaje

// ❌ MAL: Crear client cada vez
foreach (var message in messages)
{
    var client = new ServiceBusClient(connectionString);  // Costoso!
    var sender = client.CreateSender(queueName);
    await sender.SendMessageAsync(message);
}
```

#### 5. Security Best Practices

```csharp
// ✅ Usar Managed Identity (sin connection string)
var credential = new DefaultAzureCredential();
var client = new ServiceBusClient(
    "myapp.servicebus.windows.net",
    credential
);

// ✅ Least privilege con RBAC
// Azure Service Bus Data Sender (solo enviar)
// Azure Service Bus Data Receiver (solo recibir)
// Azure Service Bus Data Owner (full access)

// ✅ Connection string desde Key Vault
var secretClient = new SecretClient(vaultUri, credential);
var connectionString = await secretClient.GetSecretAsync("ServiceBusConnection");
var client = new ServiceBusClient(connectionString.Value.Value);

// ✅ VNet integration (Premium tier)
// Service Bus solo accesible desde VNet privada

// ✅ IP Firewall (Premium tier)
await managementClient.CreateOrUpdateNamespaceAsync(
    resourceGroup,
    namespaceName,
    new ServiceBusNamespaceResource
    {
        NetworkRuleSets = new NetworkRuleSet
        {
            DefaultAction = "Deny",
            IpRules = new[]
            {
                new NWRuleSetIpRules { IpMask = "203.0.113.0/24", Action = "Allow" }
            }
        }
    }
);
```

#### 6. Idempotency Pattern

```csharp
// ✅ Habilitar duplicate detection en namespace
await administrationClient.CreateQueueAsync(new CreateQueueOptions("myqueue")
{
    RequiresDuplicateDetection = true,
    DuplicateDetectionHistoryTimeWindow = TimeSpan.FromHours(1)
});

// ✅ Usar MessageId único
var message = new ServiceBusMessage("data")
{
    MessageId = Guid.NewGuid().ToString()  // O hash del contenido
};

// ✅ Application-level idempotency
private readonly HashSet<string> processedMessages = new HashSet<string>();

processor.ProcessMessageAsync += async args =>
{
    var messageId = args.Message.MessageId;
    
    if (processedMessages.Contains(messageId))
    {
        // Ya procesado, solo completar
        await args.CompleteMessageAsync(args.Message);
        return;
    }
    
    await ProcessMessageAsync(args.Message);
    processedMessages.Add(messageId);
    await args.CompleteMessageAsync(args.Message);
};
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear Service Bus Namespace

**Azure CLI:**
```bash
# Variables
RESOURCE_GROUP="rg-servicebus-prod"
LOCATION="eastus"
NAMESPACE="sb-myapp-prod"
QUEUE_NAME="orders-queue"
TOPIC_NAME="notifications"

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear namespace (Standard tier)
az servicebus namespace create \
  --resource-group $RESOURCE_GROUP \
  --name $NAMESPACE \
  --location $LOCATION \
  --sku Standard

# Crear queue
az servicebus queue create \
  --resource-group $RESOURCE_GROUP \
  --namespace-name $NAMESPACE \
  --name $QUEUE_NAME \
  --max-delivery-count 5 \
  --lock-duration PT1M \
  --default-message-time-to-live P14D \
  --enable-duplicate-detection true \
  --duplicate-detection-history-time-window PT10M

# Crear topic
az servicebus topic create \
  --resource-group $RESOURCE_GROUP \
  --namespace-name $NAMESPACE \
  --name $TOPIC_NAME

# Crear subscription con filtro
az servicebus topic subscription create \
  --resource-group $RESOURCE_GROUP \
  --namespace-name $NAMESPACE \
  --topic-name $TOPIC_NAME \
  --name email-subscription

az servicebus topic subscription rule create \
  --resource-group $RESOURCE_GROUP \
  --namespace-name $NAMESPACE \
  --topic-name $TOPIC_NAME \
  --subscription-name email-subscription \
  --name EmailFilter \
  --filter-sql-expression "NotificationType = 'Email'"

# Obtener connection string
az servicebus namespace authorization-rule keys list \
  --resource-group $RESOURCE_GROUP \
  --namespace-name $NAMESPACE \
  --name RootManageSharedAccessKey \
  --query primaryConnectionString -o tsv
```

**Premium tier con features avanzadas:**
```bash
# Crear Premium namespace
az servicebus namespace create \
  --resource-group $RESOURCE_GROUP \
  --name sb-premium-prod \
  --location $LOCATION \
  --sku Premium \
  --capacity 1 \
  --zone-redundant true

# Habilitar geo-disaster recovery
az servicebus georecovery-alias create \
  --resource-group $RESOURCE_GROUP \
  --namespace-name sb-premium-prod \
  --alias sb-premium-dr \
  --partner-namespace /subscriptions/.../sb-premium-secondary

# Configurar VNet integration
az servicebus namespace network-rule add \
  --resource-group $RESOURCE_GROUP \
  --namespace-name sb-premium-prod \
  --subnet /subscriptions/.../subnets/servicebus-subnet
```

**Terraform:**
```hcl
resource "azurerm_servicebus_namespace" "sb" {
  name                = "sb-myapp-prod"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "Standard"
  
  # Premium features
  # sku                 = "Premium"
  # capacity            = 1
  # zone_redundant      = true

  tags = {
    Environment = "Production"
  }
}

resource "azurerm_servicebus_queue" "orders" {
  name         = "orders-queue"
  namespace_id = azurerm_servicebus_namespace.sb.id

  max_delivery_count                = 5
  lock_duration                     = "PT1M"
  default_message_ttl               = "P14D"
  max_size_in_megabytes            = 1024
  requires_duplicate_detection      = true
  duplicate_detection_history_time_window = "PT10M"
  requires_session                  = false
  dead_lettering_on_message_expiration = true
  
  enable_partitioning              = true  # Solo Standard
  enable_express                   = false
}

resource "azurerm_servicebus_topic" "notifications" {
  name         = "notifications"
  namespace_id = azurerm_servicebus_namespace.sb.id

  max_size_in_megabytes = 1024
  default_message_ttl   = "P14D"
  requires_duplicate_detection = true
  enable_partitioning   = true
}

resource "azurerm_servicebus_subscription" "email" {
  name               = "email-subscription"
  topic_id           = azurerm_servicebus_topic.notifications.id
  max_delivery_count = 5
  lock_duration      = "PT1M"
  
  dead_lettering_on_message_expiration = true
}

resource "azurerm_servicebus_subscription_rule" "email_filter" {
  name            = "EmailFilter"
  subscription_id = azurerm_servicebus_subscription.email.id
  filter_type     = "SqlFilter"
  sql_filter      = "NotificationType = 'Email'"
}

# Monitoring
resource "azurerm_monitor_diagnostic_setting" "sb_diagnostics" {
  name                       = "sb-diagnostics"
  target_resource_id         = azurerm_servicebus_namespace.sb.id
  log_analytics_workspace_id = azurerm_log_analytics_workspace.logs.id

  enabled_log {
    category = "OperationalLogs"
  }

  metric {
    category = "AllMetrics"
  }
}
```

### 💻 Ejemplo Completo - C# (.NET)

**Program.cs (Producer):**
```csharp
using Azure.Messaging.ServiceBus;

var connectionString = Environment.GetEnvironmentVariable("ServiceBusConnection");
var queueName = "orders-queue";

await using var client = new ServiceBusClient(connectionString);
await using var sender = client.CreateSender(queueName);

// Send single message
var message = new ServiceBusMessage("Order data")
{
    MessageId = Guid.NewGuid().ToString(),
    ContentType = "application/json",
    CorrelationId = "correlation-123",
    ApplicationProperties =
    {
        { "OrderType", "Express" },
        { "Priority", 1 }
    },
    TimeToLive = TimeSpan.FromHours(24)
};

await sender.SendMessageAsync(message);
Console.WriteLine("Message sent");

// Send batch
var batch = await sender.CreateMessageBatchAsync();
for (int i = 0; i < 100; i++)
{
    var msg = new ServiceBusMessage($"Order {i}");
    if (!batch.TryAddMessage(msg))
    {
        await sender.SendMessagesAsync(batch);
        batch = await sender.CreateMessageBatchAsync();
        batch.TryAddMessage(msg);
    }
}
await sender.SendMessagesAsync(batch);
Console.WriteLine("Batch sent");
```

**Consumer with Processor:**
```csharp
using Azure.Messaging.ServiceBus;

var client = new ServiceBusClient(connectionString);
var processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions
{
    MaxConcurrentCalls = 10,
    AutoCompleteMessages = false,
    PrefetchCount = 100,
    MaxAutoLockRenewalDuration = TimeSpan.FromMinutes(5)
});

processor.ProcessMessageAsync += async args =>
{
    try
    {
        var body = args.Message.Body.ToString();
        var orderType = args.Message.ApplicationProperties["OrderType"];
        
        Console.WriteLine($"Processing: {body}, Type: {orderType}");
        
        // Simulate processing
        await Task.Delay(100);
        
        // Complete message
        await args.CompleteMessageAsync(args.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
        
        // Abandon para reintentar
        await args.AbandonMessageAsync(args.Message);
    }
};

processor.ProcessErrorAsync += args =>
{
    Console.WriteLine($"Error: {args.Exception.Message}");
    return Task.CompletedTask;
};

await processor.StartProcessingAsync();
Console.WriteLine("Processing started. Press any key to stop.");
Console.ReadKey();

await processor.StopProcessingAsync();
await processor.DisposeAsync();
await client.DisposeAsync();
```

**Topic Publisher:**
```csharp
await using var topicSender = client.CreateSender("notifications");

var notification = new ServiceBusMessage(JsonSerializer.Serialize(new
{
    Title = "Welcome",
    Body = "Welcome to our service!"
}))
{
    ApplicationProperties =
    {
        { "NotificationType", "Email" },
        { "Priority", 1 }
    }
};

await topicSender.SendMessageAsync(notification);
```

**Subscription Consumer:**
```csharp
// Solo recibe mensajes que pasan el filtro SQL
var processor = client.CreateProcessor("notifications", "email-subscription");

processor.ProcessMessageAsync += async args =>
{
    var notificationType = args.Message.ApplicationProperties["NotificationType"];
    Console.WriteLine($"Received notification: {notificationType}");
    
    await args.CompleteMessageAsync(args.Message);
};

await processor.StartProcessingAsync();
```

### 💻 Ejemplo Spring Boot (Java)

**pom.xml:**
```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>spring-cloud-azure-starter-servicebus-jms</artifactId>
    <version>5.8.0</version>
</dependency>
```

**application.yml:**
```yaml
spring:
  jms:
    servicebus:
      connection-string: ${SERVICE_BUS_CONNECTION_STRING}
      pricing-tier: standard
```

**Producer:**
```java
@Service
public class OrderProducer {
    
    @Autowired
    private JmsTemplate jmsTemplate;
    
    public void sendOrder(Order order) {
        jmsTemplate.convertAndSend("orders-queue", order, message -> {
            message.setStringProperty("OrderType", order.getType());
            message.setIntProperty("Priority", order.getPriority());
            message.setJMSCorrelationID(order.getCorrelationId());
            return message;
        });
    }
}
```

**Consumer:**
```java
@Component
public class OrderConsumer {
    
    @JmsListener(destination = "orders-queue")
    public void receiveOrder(Order order, Message message) throws JMSException {
        String orderType = message.getStringProperty("OrderType");
        System.out.println("Processing order: " + order.getId() + ", Type: " + orderType);
        
        // Process order
        processOrder(order);
    }
}
```

---

Esta es la guía completa de Azure Service Bus. Quedan 12 servicios por documentar. ¿Continúo con **Azure Event Hubs**? 📡