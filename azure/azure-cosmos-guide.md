# 🌌 Azure Cosmos DB - Guía Completa

## 📖 Definición Detallada

Azure Cosmos DB es una **base de datos NoSQL completamente administrada, multi-modelo y globalmente distribuida** diseñada para aplicaciones que requieren baja latencia (<10ms), alta disponibilidad (99.999%), escalabilidad elástica y distribución global automática. Es la evolución de Azure DocumentDB con capacidades multi-API.

**Arquitectura fundamental:**

**Multi-modelo (múltiples APIs):**
- **NoSQL API (Core):** Documentos JSON con queries SQL-like
- **MongoDB API:** Compatible con MongoDB wire protocol (v3.6, 4.0, 4.2, 5.0, 6.0)
- **Cassandra API:** Compatible con Apache Cassandra (CQL)
- **Gremlin API:** Base de datos de grafos (Apache TinkerPop)
- **Table API:** Key-value simple (sucesor de Azure Table Storage)
- **PostgreSQL API (Preview):** Compatible con PostgreSQL wire protocol

**Conceptos clave:**

**1. Distribución Global:**
- **Multi-region replication:** Datos replicados automáticamente en múltiples regiones
- **Multi-master (multi-region writes):** Escribe en cualquier región, sincronización automática
- **Automatic failover:** Si una región cae, otra toma el control en segundos
- **Geo-redundancia:** Datos replicados dentro de cada región en múltiples Availability Zones

**2. Particionamiento:**
- **Partition Key:** Campo que determina cómo se distribuyen los datos
- **Logical Partitions:** Agrupación de datos con mismo partition key (max 20 GB)
- **Physical Partitions:** Unidad de almacenamiento y throughput (10 GB - 50 GB)
- **Cross-partition queries:** Queries que tocan múltiples particiones (más costosos)

**3. Request Units (RUs):**
- **Unidad de throughput:** 1 RU = 1 read de 1 KB o ~5 writes de 1 KB
- **Provisioned Throughput:** RUs asignados por adelantado (más barato si uso predecible)
- **Serverless:** Pagas solo por RUs consumidos (ideal para dev/test o tráfico esporádico)
- **Autoscale:** RUs se ajustan automáticamente entre min y max

**4. Consistency Levels (niveles de consistencia):**
- **Strong:** Lectura siempre retorna la última escritura (latencia más alta)
- **Bounded Staleness:** Lectura puede estar desfasada por K versiones o T segundos
- **Session:** Consistencia garantizada dentro de la misma sesión de cliente
- **Consistent Prefix:** Lecturas nunca ven escrituras fuera de orden
- **Eventual:** Consistencia eventual (latencia más baja, throughput más alto)

**5. Indexación:**
- **Automatic indexing:** Todos los campos indexados por defecto
- **Inverted index:** Para queries rápidos
- **Composite indexes:** Para queries multi-campo eficientes
- **Spatial indexes:** Para queries geoespaciales
- **Excluded paths:** Optimizar costos excluyendo campos que nunca se queran

### 🎯 Importancia

**Por qué Cosmos DB es crítico para aplicaciones globales:**

1. **Latencia ultra-baja:** <10ms en P99 desde cualquier región
2. **Disponibilidad extrema:** 99.999% SLA con multi-region writes
3. **Escalabilidad ilimitada:** De 0 a millones de RUs sin downtime
4. **Distribución global transparente:** Replica datos en 60+ regiones con 1 click
5. **Multi-modelo:** Una base de datos para múltiples workloads
6. **Schema-less:** No necesita migraciones de schema al evolucionar datos
7. **Garantías SLA:** Latencia, throughput, consistencia y disponibilidad garantizadas
8. **Time-to-market:** Sin gestión de infraestructura, backups automáticos
9. **Change Feed:** Stream de cambios en tiempo real para event-driven architectures
10. **Analytics integrado:** Synapse Link para queries analíticos sin impactar OLTP

### ✨ Características Principales

**1. Distribución Global:**
- **Single-click replication:** Agregar regiones desde portal
- **Multi-region writes:** Escribe en la región más cercana al usuario
- **Automatic conflict resolution:** Last-Write-Wins, Custom, Async procedure
- **Region failover:** Manual o automático (priority basado)
- **Geo-fencing:** Controlar en qué regiones se replican los datos

**2. Throughput y Escalabilidad:**
- **Provisioned throughput:**
  - Database-level: Compartido entre todos los containers
  - Container-level: Dedicado por container
  - Autoscale: Min 100 RU/s, max 1M+ RU/s
- **Serverless:** Sin provisionar, pagas por uso
- **Elastic scale-out:** Agrega particiones automáticamente cuando creces
- **Unlimited storage:** Escala sin límite teórico

**3. Indexación Avanzada:**
```json
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    { "path": "/*" }
  ],
  "excludedPaths": [
    { "path": "/largeText/*" },
    { "path": "/_etag/?" }
  ],
  "compositeIndexes": [
    [
      { "path": "/category", "order": "ascending" },
      { "path": "/price", "order": "descending" }
    ]
  ],
  "spatialIndexes": [
    { "path": "/location/*", "types": ["Point", "Polygon"] }
  ]
}
```

**4. Queries Avanzados:**
```sql
-- SQL API (NoSQL)
SELECT c.name, c.email, c.orders
FROM customers c
WHERE c.country = 'USA' 
  AND ARRAY_LENGTH(c.orders) > 5
ORDER BY c.lastOrderDate DESC
OFFSET 0 LIMIT 10

-- Aggregations
SELECT VALUE COUNT(1) 
FROM c 
WHERE c.category = 'Electronics'

-- Joins (dentro del documento)
SELECT p.name, o.quantity
FROM products p
JOIN o IN p.orders
WHERE o.quantity > 10

-- Geospatial
SELECT * 
FROM c 
WHERE ST_DISTANCE(c.location, {'type': 'Point', 'coordinates': [40.7, -74.0]}) < 5000
```

**5. Change Feed:**
- Stream de cambios ordenado y persistente
- Consume cambios como eventos
- Checkpoint automático (mantiene posición)
- Procesamiento paralelo por partition key range
- Casos de uso: Event sourcing, CQRS, real-time analytics, cache invalidation

**6. Transacciones:**
- **ACID en logical partition:** Transacciones dentro del mismo partition key
- **Stored Procedures:** JavaScript ejecutado en el servidor atómicamente
- **Triggers:** Pre-triggers y post-triggers
- **User-Defined Functions (UDFs):** Lógica custom en queries

**7. Time-to-Live (TTL):**
- Expiración automática de documentos
- TTL a nivel de container (default) o por documento
- Útil para datos temporales (sessions, logs, cache)

**8. Analytical Store (Synapse Link):**
- Column-store automático y sincronizado
- Queries analíticos sin impacto en OLTP
- Compatible con Synapse Analytics, Spark, Power BI
- Auto-sync incremental (near real-time)

### ✅ Ventajas

1. **Global por diseño:** Replica en múltiples regiones con facilidad
2. **SLA incomparable:** 99.999% availability con multi-region writes
3. **Latencia garantizada:** <10ms P99 (lectura/escritura)
4. **Consistencia configurable:** 5 niveles para balance performance/consistency
5. **Multi-modelo:** Una DB para documentos, grafos, key-value, columnar
6. **Sin límites de escala:** Storage y throughput ilimitados
7. **Schema-agnostic:** JSON flexible, evoluciona sin downtime
8. **Completamente administrado:** Backups automáticos, patching, HA
9. **Change Feed nativo:** Event-driven sin infraestructura adicional
10. **Serverless option:** Ideal para dev/test o apps con tráfico esporádico
11. **Analytics integrado:** Synapse Link para BI sin ETL

### ❌ Desventajas

1. **Costo elevado:** RUs son caros comparado con VMs + MongoDB/Cassandra
2. **Complejidad de particionamiento:** Elegir mal partition key = hot partitions
3. **Cross-partition queries costosos:** Queries que tocan múltiples particiones consumen muchos RUs
4. **Límite de partition key:** 20 GB por logical partition (puede ser limitante)
5. **Curva de aprendizaje:** RUs, partition keys, consistency levels
6. **No es relacional:** Sin JOINs reales entre colecciones (solo intra-documento)
7. **Stored procedures en JavaScript:** No TypeScript, debugging difícil
8. **Tamaño de documento:** 2 MB máximo por documento
9. **Transacciones limitadas:** Solo dentro del mismo partition key
10. **Migration complexity:** Migrar de SQL relacional requiere rediseño de schema
11. **Throttling inesperado:** Si excedes RUs provisionados, requests son rechazados (429)
12. **Lock-in de Azure:** APIs propietarias dificultan migración (excepto MongoDB/Cassandra APIs)

### 🎯 Cuándo Aplicarlo

**✅ USAR Azure Cosmos DB cuando:**

1. **Aplicación global:** Usuarios en múltiples continentes requieren baja latencia
2. **Alta disponibilidad crítica:** 99.99% no es suficiente, necesitas 99.999%
3. **Escalabilidad masiva:** Crecimiento impredecible (viral, seasonal spikes)
4. **Baja latencia obligatoria:** <10ms en P99 es un requisito del negocio
5. **Schema flexible:** Datos semi-estructurados que evolucionan rápidamente
6. **IoT a escala:** Millones de dispositivos escribiendo telemetría
7. **Gaming:** Leaderboards globales, matchmaking, session state
8. **E-commerce global:** Catálogo, carrito, orders distribuidos globalmente
9. **Real-time analytics:** Dashboards en vivo con Change Feed
10. **Event sourcing / CQRS:** Change Feed como event log
11. **Mobile/web apps:** Backend for mobile con offline sync
12. **Multi-tenancy:** Aislamiento por partition key con scale independiente

**Ejemplos específicos:**
- **Uber:** Tracking de vehículos en tiempo real, geo-queries
- **Xbox:** Perfiles de jugadores, leaderboards globales
- **Schneider Electric:** IoT telemetry de millones de dispositivos
- **Walgreens:** Catálogo de productos y órdenes en 9,000+ tiendas
- **Chipotle:** Sistema de órdenes online con picos de tráfico

**❌ NO USAR Azure Cosmos DB cuando:**

1. **Aplicación regional:** Usuarios solo en una región → Azure SQL más barato
2. **Presupuesto limitado:** Cosmos DB es caro para workloads pequeños
3. **Datos relacionales complejos:** Múltiples tablas con JOINs → Azure SQL
4. **ACID multi-entidad crítico:** Transacciones distribuidas → SQL Server
5. **Reportes complejos:** Queries con múltiples JOINs → Azure SQL + Analytics
6. **Datos de archivos grandes:** BLOBs, imágenes → Azure Storage
7. **Full-text search avanzado:** Cognitive Search es mejor opción
8. **Data warehouse:** Grandes volúmenes analíticos → Synapse Analytics
9. **Graph queries complejos:** Si solo usas grafos → Neo4j puede ser mejor
10. **Latencia no es crítica:** Si 50-100ms es aceptable, Azure SQL es más barato
11. **Workload estable y predecible:** Si no necesitas elasticidad, VMs + MongoDB es más barato
12. **Compliance on-premises:** Regulaciones que prohíben cloud público

### 📋 Situaciones Específicas

**Escenario 1: Blog personal con 100 visitas/día**
- ❌ Cosmos DB: Overkill y caro ($24-50/mes mínimo en serverless)
- ✅ Alternativa: Azure SQL Database Basic ($5/mes) o MongoDB Atlas free tier

**Escenario 2: Startup SaaS B2B con 1,000 clientes**
- ✅ Cosmos DB Serverless: Crecimiento impredecible, paga por uso
- ⚠️ Cuando superes $100/mes, evaluar provisioned throughput

**Escenario 3: E-commerce con Black Friday traffic**
- ✅ Cosmos DB Autoscale: Escala automáticamente durante picos

**Escenario 4: Sistema bancario con transacciones ACID**
- ❌ Cosmos DB: Transacciones solo dentro de partition key
- ✅ Azure SQL Database con Always Encrypted

**Escenario 5: Sistema de recomendaciones ML**
- ✅ Cosmos DB: Cache de recomendaciones, baja latencia
- ✅ Synapse Link: Training de modelos sin impactar producción

**Escenario 6: Aplicación IoT con 100k sensores**
- ✅ Cosmos DB: Ingest masivo, partition key = deviceId
- ⚠️ Para histórico: Archive a Azure Data Lake

**Escenario 7: CRM corporativo interno**
- ❌ Cosmos DB: No necesita global distribution
- ✅ Azure SQL Database: Más barato y queries relacionales

**Escenario 8: Red social global (estilo Twitter)**
- ✅ Cosmos DB: Timelines, feeds, baja latencia global
- ✅ Change Feed: Notificaciones en tiempo real

### 🔧 Políticas y Mejores Prácticas

#### 1. Elección de Partition Key

**Criterios para una buena partition key:**
- **Alta cardinalidad:** Muchos valores únicos (evita hot partitions)
- **Distribución uniforme:** Requests distribuidos equitativamente
- **Query pattern:** Queries frecuentes deben incluir partition key
- **Tamaño de partition:** Cada logical partition < 20 GB

**Ejemplos:**

```javascript
// ❌ MAL: Baja cardinalidad
{
  "id": "user-123",
  "partitionKey": "active"  // Solo 2 valores: active/inactive
}

// ❌ MAL: Distribución no uniforme
{
  "id": "order-456",
  "partitionKey": "USA"  // 80% de requests en USA, hot partition
}

// ✅ BIEN: Alta cardinalidad, distribución uniforme
{
  "id": "order-456",
  "partitionKey": "user-123"  // Cada usuario es una partition
}

// ✅ BIEN: Composite partition key (manual)
{
  "id": "order-456",
  "partitionKey": "user-123-2024-01"  // userId + año-mes
}

// ✅ BIEN: Para IoT
{
  "id": "telemetry-789",
  "partitionKey": "device-123"  // deviceId
}

// ✅ BIEN: Para multi-tenancy
{
  "id": "customer-data",
  "partitionKey": "tenant-abc"  // tenantId
}
```

#### 2. Indexing Policy Optimization

**Default policy (indexa todo):**
```json
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    { "path": "/*" }
  ],
  "excludedPaths": []
}
```

**Optimizado (excluye campos grandes):**
```json
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    { "path": "/name/?" },
    { "path": "/email/?" },
    { "path": "/createdDate/?" },
    { "path": "/status/?" }
  ],
  "excludedPaths": [
    { "path": "/description/*" },  // Texto largo
    { "path": "/largeObject/*" },  // Objeto nested grande
    { "path": "/_etag/?" }         // System property
  ],
  "compositeIndexes": [
    [
      { "path": "/status", "order": "ascending" },
      { "path": "/createdDate", "order": "descending" }
    ]
  ]
}
```

**Beneficios:**
- Reduce RUs de escritura (menos campos que indexar)
- Reduce storage costs
- Queries con composite index son más eficientes

#### 3. Query Optimization

**❌ Evitar:**
```sql
-- Cross-partition query sin filtro
SELECT * FROM c

-- Sin ORDER BY en pagination (inconsistente)
SELECT * FROM c OFFSET 10 LIMIT 10

-- Agregación sin partition key
SELECT COUNT(1) FROM c WHERE c.status = 'active'
```

**✅ Optimizado:**
```sql
-- Incluir partition key cuando sea posible
SELECT * FROM c 
WHERE c.userId = 'user-123' AND c.status = 'active'

-- Usar continuation token para pagination
SELECT * FROM c 
WHERE c.userId = 'user-123'
ORDER BY c.createdDate DESC

-- Limitar campos en SELECT
SELECT c.id, c.name, c.email FROM c 
WHERE c.userId = 'user-123'

-- Usar IN para múltiples partition keys (más eficiente que OR)
SELECT * FROM c 
WHERE c.userId IN ('user-1', 'user-2', 'user-3')
```

#### 4. Consistency Level Selection

```
Performance ←→ Consistency

Eventual < Consistent Prefix < Session < Bounded Staleness < Strong

Uso de RU: Bajo → Alto
Latencia: Baja → Alta
```

**Recomendaciones por caso de uso:**

| Caso de Uso | Consistency Level | Por qué |
|-------------|-------------------|---------|
| Social media timeline | Eventual o Session | OK ver posts con segundos de retraso |
| E-commerce inventory | Bounded Staleness | Balance entre performance y accuracy |
| User profile (own data) | Session | Consistencia en misma sesión |
| Financial transactions | Strong | Requiere lectura de última escritura |
| Gaming leaderboard | Eventual | OK si leaderboard tiene segundos de retraso |
| Shopping cart | Session | Usuario ve sus propios cambios inmediatamente |
| Analytics dashboard | Eventual | Datos pueden estar desfasados minutos |

#### 5. TTL (Time-to-Live)

**A nivel de container:**
```json
{
  "id": "users",
  "partitionKey": "/userId",
  "defaultTtl": 86400  // 24 horas
}
```

**Por documento (override):**
```json
{
  "id": "session-123",
  "userId": "user-456",
  "data": {},
  "ttl": 3600  // 1 hora
}

// -1 = nunca expira (override del default)
{
  "id": "user-profile",
  "ttl": -1
}
```

**Casos de uso:**
- **Sessions:** TTL = session timeout (30 min - 2 horas)
- **Cache:** TTL = cache invalidation time (5 min - 1 hora)
- **Logs:** TTL = retention period (7-30 días)
- **Temporary data:** Orders en carrito (24 horas sin checkout)

#### 6. Change Feed Patterns

**Pattern 1: Event Sourcing**
```java
// Leer todos los cambios desde el inicio
ChangeFeedProcessor processor = new ChangeFeedProcessorBuilder()
    .hostName("processor-1")
    .feedContainer(sourceContainer)
    .leaseContainer(leaseContainer)
    .handleChanges(docs -> {
        for (JsonNode doc : docs) {
            // Publicar evento a Event Hub o Service Bus
            eventPublisher.publish(doc);
        }
    })
    .buildChangeFeedProcessor();
```

**Pattern 2: Materialized Views**
```java
// Actualizar vista materializada en otro container
.handleChanges(docs -> {
    for (JsonNode doc : docs) {
        if (doc.get("type").asText().equals("Order")) {
            // Actualizar vista de órdenes por usuario
            updateUserOrderSummary(doc);
        }
    }
})
```

**Pattern 3: Cache Invalidation**
```java
// Invalidar cache cuando datos cambian
.handleChanges(docs -> {
    for (JsonNode doc : docs) {
        String cacheKey = "product:" + doc.get("id").asText();
        redisClient.del(cacheKey);
    }
})
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear Cosmos DB Account

**Opción A: Azure CLI (NoSQL API)**
```bash
# Variables
RESOURCE_GROUP="rg-cosmos-prod"
LOCATION="eastus"
ACCOUNT_NAME="cosmos-myapp-prod"
DATABASE_NAME="myapp"
CONTAINER_NAME="users"

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear Cosmos DB account (NoSQL API)
az cosmosdb create \
  --name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --locations regionName=$LOCATION failoverPriority=0 isZoneRedundant=True \
  --default-consistency-level Session \
  --enable-automatic-failover true \
  --enable-multiple-write-locations false

# Crear database con shared throughput (400 RU/s mínimo)
az cosmosdb sql database create \
  --account-name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --name $DATABASE_NAME \
  --throughput 400

# Crear container con partition key
az cosmosdb sql container create \
  --account-name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --database-name $DATABASE_NAME \
  --name $CONTAINER_NAME \
  --partition-key-path "/userId" \
  --throughput 400

# Obtener connection string
az cosmosdb keys list \
  --name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --type connection-strings \
  --query "connectionStrings[0].connectionString" -o tsv
```

**Opción B: Multi-region con Autoscale**
```bash
# Crear con múltiples regiones
az cosmosdb create \
  --name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --locations \
    regionName=eastus failoverPriority=0 isZoneRedundant=True \
    regionName=westus failoverPriority=1 isZoneRedundant=True \
    regionName=westeurope failoverPriority=2 isZoneRedundant=True \
  --default-consistency-level BoundedStaleness \
  --max-staleness-prefix 100 \
  --max-interval 5 \
  --enable-automatic-failover true \
  --enable-multiple-write-locations true

# Container con autoscale (min 100, max 10,000 RU/s)
az cosmosdb sql container create \
  --account-name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --database-name $DATABASE_NAME \
  --name $CONTAINER_NAME \
  --partition-key-path "/userId" \
  --max-throughput 10000  # Autoscale
```

**Opción C: Serverless**
```bash
az cosmosdb create \
  --name cosmos-myapp-serverless \
  --resource-group $RESOURCE_GROUP \
  --locations regionName=$LOCATION \
  --capabilities EnableServerless \
  --default-consistency-level Session

# Database y container sin throughput
az cosmosdb sql database create \
  --account-name cosmos-myapp-serverless \
  --resource-group $RESOURCE_GROUP \
  --name $DATABASE_NAME

az cosmosdb sql container create \
  --account-name cosmos-myapp-serverless \
  --resource-group $RESOURCE_GROUP \
  --database-name $DATABASE_NAME \
  --name $CONTAINER_NAME \
  --partition-key-path "/userId"
  # No se especifica --throughput en serverless
```

**Opción D: Terraform**
```hcl
# main.tf
resource "azurerm_resource_group" "rg" {
  name     = "rg-cosmos-prod"
  location = "eastus"
}

resource "azurerm_cosmosdb_account" "cosmos" {
  name                = "cosmos-myapp-prod"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"  # NoSQL API

  consistency_policy {
    consistency_level       = "BoundedStaleness"
    max_interval_in_seconds = 5
    max_staleness_prefix    = 100
  }

  # Multi-region
  geo_location {
    location          = "eastus"
    failover_priority = 0
    zone_redundant    = true
  }

  geo_location {
    location          = "westus"
    failover_priority = 1
    zone_redundant    = true
  }

  geo_location {
    location          = "westeurope"
    failover_priority = 2
    zone_redundant    = true
  }

  enable_automatic_failover         = true
  enable_multiple_write_locations   = true
  enable_free_tier                  = false
  
  # Backup
  backup {
    type                = "Continuous"
    interval_in_minutes = 240
    retention_in_hours  = 720
  }

  # Network
  is_virtual_network_filter_enabled = true
  public_network_access_enabled     = false

  virtual_network_rule {
    id = azurerm_subnet.cosmos_subnet.id
  }

  tags = {
    Environment = "Production"
    ManagedBy   = "Terraform"
  }
}

resource "azurerm_cosmosdb_sql_database" "db" {
  name                = "myapp"
  resource_group_name = azurerm_cosmosdb_account.cosmos.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmos.name
  
  # Database-level throughput (compartido)
  throughput          = 400
}

resource "azurerm_cosmosdb_sql_container" "users" {
  name                  = "users"
  resource_group_name   = azurerm_cosmosdb_account.cosmos.resource_group_name
  account_name          = azurerm_cosmosdb_account.cosmos.name
  database_name         = azurerm_cosmosdb_sql_database.db.name
  partition_key_path    = "/userId"
  partition_key_version = 1
  
  # Container-level throughput con autoscale
  autoscale_settings {
    max_throughput = 4000
  }

  indexing_policy {
    indexing_mode = "consistent"

    included_path {
      path = "/*"
    }

    excluded_path {
      path = "/description/*"
    }

    excluded_path {
      path = "/_etag/?"
    }

    composite_index {
      index {
        path  = "/userId"
        order = "ascending"
      }

      index {
        path  = "/createdDate"
        order = "descending"
      }
    }
  }

  # TTL habilitado
  default_ttl = -1  # -1 = no expira por default, pero permite override por documento

  # Unique keys
  unique_key {
    paths = ["/email"]
  }
}

resource "azurerm_cosmosdb_sql_container" "orders" {
  name                = "orders"
  resource_group_name = azurerm_cosmosdb_account.cosmos.resource_group_name
  account_name        = azurerm_cosmosdb_account.cosmos.name
  database_name       = azurerm_cosmosdb_sql_database.db.name
  partition_key_path  = "/userId"
  
  # Hereda throughput de la database (compartido)

  default_ttl = 2592000  # 30 días

  indexing_policy {
    indexing_mode = "consistent"

    included_path {
      path = "/*"
    }

    composite_index {
      index {
        path  = "/userId"
        order = "ascending"
      }

      index {
        path  = "/orderDate"
        order = "descending"
      }
    }
  }
}

# Monitoring
resource "azurerm_log_analytics_workspace" "logs" {
  name                = "logs-cosmos-prod"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}

resource "azurerm_monitor_diagnostic_setting" "cosmos_diagnostics" {
  name                       = "cosmos-diagnostics"
  target_resource_id         = azurerm_cosmosdb_account.cosmos.id
  log_analytics_workspace_id = azurerm_log_analytics_workspace.logs.id

  enabled_log {
    category = "DataPlaneRequests"
  }

  enabled_log {
    category = "QueryRuntimeStatistics"
  }

  enabled_log {
    category = "PartitionKeyStatistics"
  }

  metric {
    category = "Requests"
  }
}

# Outputs
output "cosmos_endpoint" {
  value = azurerm_cosmosdb_account.cosmos.endpoint
}

output "cosmos_primary_key" {
  value     = azurerm_cosmosdb_account.cosmos.primary_key
  sensitive = true
}

output "cosmos_connection_string" {
  value     = azurerm_cosmosdb_account.cosmos.connection_strings[0]
  sensitive = true
}
```

#### Paso 2: Configurar Indexing Policy

```bash
# Actualizar indexing policy de un container existente
az cosmosdb sql container update \
  --account-name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --database-name $DATABASE_NAME \
  --name $CONTAINER_NAME \
  --idx @indexing-policy.json

# indexing-policy.json
cat > indexing-policy.json << 'EOF'
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/*"
    }
  ],
  "excludedPaths": [
    {
      "path": "/largeDescription/*"
    },
    {
      "path": "/_etag/?"
    }
  ],
  "compositeIndexes": [
    [
      {
        "path": "/userId",
        "order": "ascending"
      },
      {
        "path": "/createdDate",
        "order": "descending"
      }
    ]
  ]
}
EOF
```

#### Paso 3: Habilitar Change Feed

Change Feed está habilitado automáticamente, solo necesitas consumirlo desde tu aplicación (ver ejemplos de código abajo).

#### Paso 4: Configurar Backup

**Continuous Backup (point-in-time restore):**
```bash
az cosmosdb update \
  --name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --backup-policy-type Continuous

# Restaurar a un punto en el tiempo
az cosmosdb restore \
  --target-database-account-name cosmos-myapp-restored \
  --account-name $ACCOUNT_NAME \
  --resource-group $RESOURCE_GROUP \
  --restore-timestamp "2024-01-15T10:30:00Z" \
  --location eastus
```

### 💻 Ejemplo Completo con Spring Boot

#### 1. Dependencias Maven

**pom.xml:**
```xml
<dependencies>
    <!-- Spring Boot -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Azure Cosmos DB Spring Data -->
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-spring-data-cosmos</artifactId>
        <version>5.8.0</version>
    </dependency>
    
    <!-- Azure Identity (para Managed Identity) -->
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.11.0</version>
    </dependency>
</dependencies>
```

#### 2. Configuración

**application.yml:**
```yaml
spring:
  application:
    name: employee-api
  
  cloud:
    azure:
      cosmos:
        endpoint: https://cosmos-myapp-prod.documents.azure.com:443/
        key: ${COSMOS_PRIMARY_KEY}
        database: myapp
        populate-query-metrics: true

# Logging
logging:
  level:
    com.azure.cosmos: DEBUG
    com.azure.spring.data.cosmos: DEBUG
```

#### 3. Configuración de Cosmos DB

**CosmosConfig.java:**
```java
@Configuration
@EnableCosmosRepositories(basePackages = "com.empresa.repository")
@EnableReactiveCosmosRepositories(basePackages = "com.empresa.repository.reactive")
public class CosmosConfig extends AbstractCosmosConfiguration {
    
    @Value("${spring.cloud.azure.cosmos.endpoint}")
    private String endpoint;
    
    @Value("${spring.cloud.azure.cosmos.key}")
    private String key;
    
    @Value("${spring.cloud.azure.cosmos.database}")
    private String database;
    
    @Bean
    public CosmosClientBuilder cosmosClientBuilder() {
        return new CosmosClientBuilder()
            .endpoint(endpoint)
            .key(key)
            .consistencyLevel(ConsistencyLevel.SESSION)
            .connectionSharingAcrossClientsEnabled(true)
            .contentResponseOnWriteEnabled(true);
    }
    
    @Override
    protected String getDatabaseName() {
        return database;
    }
}
```

#### 4. Entity (Document)

**Employee.java:**
```java
@Container(containerName = "employees", autoCreateContainer = false)
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Employee {
    
    @Id
    private String id;
    
    @PartitionKey
    private String userId;  // Partition key
    
    private String firstName;
    private String lastName;
    private String email;
    private String department;
    private BigDecimal salary;
    
    @JsonProperty("_ts")
    private Long timestamp;  // System timestamp
    
    @JsonProperty("_etag")
    private String etag;  // Optimistic concurrency
    
    private LocalDateTime createdDate;
    private LocalDateTime lastModifiedDate;
    
    // TTL (opcional, en segundos)
    @TimeToLive
    private Integer ttl;
    
    // Campos nested
    private Address address;
    private List<String> skills;
}

@Data
public class Address {
    private String street;
    private String city;
    private String state;
    private String zipCode;
    private GeoJsonPoint location;  // Para geospatial queries
}
```

#### 5. Repository

**EmployeeRepository.java:**
```java
@Repository
public interface EmployeeRepository extends CosmosRepository<Employee, String> {
    
    // Query methods (Spring Data genera implementación)
    List<Employee> findByUserId(String userId);
    
    List<Employee> findByDepartment(String department);
    
    List<Employee> findByUserIdAndDepartment(String userId, String department);
    
    // Paginación
    Page<Employee> findByUserId(String userId, Pageable pageable);
    
    // Custom query con @Query
    @Query("SELECT * FROM c WHERE c.userId = @userId AND c.salary > @minSalary")
    List<Employee> findHighEarners(
        @Param("userId") String userId,
        @Param("minSalary") BigDecimal minSalary
    );
    
    // Aggregation
    @Query("SELECT VALUE COUNT(1) FROM c WHERE c.userId = @userId")
    Long countByUserId(@Param("userId") String userId);
    
    // Cross-partition query (costoso)
    @Query("SELECT * FROM c WHERE c.department = @dept ORDER BY c.salary DESC OFFSET @offset LIMIT @limit")
    List<Employee> findTopEarnersByDepartment(
        @Param("dept") String department,
        @Param("offset") int offset,
        @Param("limit") int limit
    );
}
```

#### 6. Service con mejores prácticas

**EmployeeService.java:**
```java
@Service
@Slf4j
public class EmployeeService {
    
    @Autowired
    private EmployeeRepository repository;
    
    @Autowired
    private CosmosTemplate cosmosTemplate;
    
    // CREATE
    public Employee createEmployee(Employee employee) {
        employee.setId(UUID.randomUUID().toString());
        employee.setCreatedDate(LocalDateTime.now());
        employee.setLastModifiedDate(LocalDateTime.now());
        
        return repository.save(employee);
    }
    
    // READ con partition key (eficiente)
    public Employee getEmployee(String id, String userId) {
        PartitionKey partitionKey = new PartitionKey(userId);
        
        return cosmosTemplate.findById(
            "employees",
            id,
            Employee.class,
            partitionKey
        );
    }
    
    // READ sin partition key (cross-partition, costoso)
    public Optional<Employee> getEmployeeById(String id) {
        return repository.findById(id);
    }
    
    // UPDATE con optimistic concurrency
    public Employee updateEmployee(Employee employee) {
        employee.setLastModifiedDate(LocalDateTime.now());
        
        try {
            return repository.save(employee);
        } catch (CosmosException e) {
            if (e.getStatusCode() == 412) {  // Precondition failed
                throw new ConcurrentModificationException(
                    "Employee was modified by another process");
            }
            throw e;
        }
    }
    
    // DELETE
    public void deleteEmployee(String id, String userId) {
        PartitionKey partitionKey = new PartitionKey(userId);
        cosmosTemplate.deleteById("employees", id, partitionKey);
    }
    
    // QUERY con paginación
    public Page<Employee> getEmployeesByUserId(
        String userId,
        int page,
        int size
    ) {
        Pageable pageable = PageRequest.of(page, size);
        return repository.findByUserId(userId, pageable);
    }
    
    // BATCH operation (dentro del mismo partition key)
    @Transactional
    public void batchUpdateEmployees(List<Employee> employees) {
        // Cosmos DB transacciones solo funcionan dentro del mismo partition key
        String userId = employees.get(0).getUserId();
        
        if (!employees.stream().allMatch(e -> e.getUserId().equals(userId))) {
            throw new IllegalArgumentException(
                "All employees must have same userId for batch operation");
        }
        
        repository.saveAll(employees);
    }
}
```

#### 7. Stored Procedure

**Crear stored procedure en Cosmos DB:**
```javascript
// bulkUpdate.js - Stored Procedure
function bulkUpdate(documents) {
    var collection = getContext().getCollection();
    var response = getContext().getResponse();
    var count = 0;
    
    if (!documents || documents.length === 0) {
        response.setBody({ updated: 0, error: "No documents provided" });
        return;
    }
    
    // Actualizar documentos uno por uno
    for (var i = 0; i < documents.length; i++) {
        var doc = documents[i];
        var accepted = collection.replaceDocument(
            collection.getAltLink() + "/docs/" + doc.id,
            doc,
            function(err, docReplaced) {
                if (err) throw err;
                count++;
                
                if (count === documents.length) {
                    response.setBody({ updated: count });
                }
            }
        );
        
        if (!accepted) break;
    }
}
```

**Ejecutar desde Java:**
```java
@Service
public class CosmosStoredProcedureService {
    
    @Autowired
    private CosmosClient cosmosClient;
    
    public void executeBulkUpdate(String userId, List<Employee> employees) {
        CosmosContainer container = cosmosClient
            .getDatabase("myapp")
            .getContainer("employees");
        
        CosmosStoredProcedure storedProcedure = 
            container.getScripts().getStoredProcedure("bulkUpdate");
        
        CosmosStoredProcedureRequestOptions options = 
            new CosmosStoredProcedureRequestOptions();
        options.setPartitionKey(new PartitionKey(userId));
        
        CosmosStoredProcedureResponse response = storedProcedure.execute(
            Collections.singletonList(employees),
            options
        );
        
        log.info("Bulk update response: {}", response.getResponseAsString());
    }
}
```

#### 8. Change Feed Processor

**ChangeFeedConfig.java:**
```java
@Configuration
public class ChangeFeedConfig {
    
    @Bean
    public ChangeFeedProcessor changeFeedProcessor(
        CosmosAsyncClient cosmosClient
    ) {
        CosmosAsyncContainer feedContainer = cosmosClient
            .getDatabase("myapp")
            .getContainer("employees");
        
        CosmosAsyncContainer leaseContainer = cosmosClient
            .getDatabase("myapp")
            .getContainer("leases");  // Container para checkpoints
        
        return new ChangeFeedProcessorBuilder()
            .hostName("employee-processor-" + UUID.randomUUID())
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges(docs -> {
                for (JsonNode doc : docs) {
                    log.info("Change detected: {}", doc.toPrettyString());
                    
                    // Procesar cambio
                    processEmployeeChange(doc);
                }
            })
            .buildChangeFeedProcessor();
    }
    
    private void processEmployeeChange(JsonNode doc) {
        String eventType = doc.has("_deleted") ? "DELETE" : "UPSERT";
        
        // Publicar evento a Event Hub o Service Bus
        // Actualizar cache en Redis
        // Actualizar vista materializada
        // etc.
    }
}

@Component
public class ChangeFeedRunner implements ApplicationRunner {
    
    @Autowired
    private ChangeFeedProcessor changeFeedProcessor;
    
    @Override
    public void run(ApplicationArguments args) {
        changeFeedProcessor.start()
            .subscribeOn(Schedulers.boundedElastic())
            .subscribe();
        
        log.info("Change Feed Processor started");
    }
    
    @PreDestroy
    public void stop() {
        changeFeedProcessor.stop().block();
        log.info("Change Feed Processor stopped");
    }
}
```

### 💻 Ejemplo con Quarkus

**pom.xml:**
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.50.0</version>
</dependency>
```

**application.properties:**
```properties
azure.cosmos.endpoint=https://cosmos-myapp-prod.documents.azure.com:443/
azure.cosmos.key=${COSMOS_PRIMARY_KEY}
azure.cosmos.database=myapp
azure.cosmos.consistency-level=SESSION
```

**CosmosClientProducer.java:**
```java
@ApplicationScoped
public class CosmosClientProducer {
    
    @ConfigProperty(name = "azure.cosmos.endpoint")
    String endpoint;
    
    @ConfigProperty(name = "azure.cosmos.key")
    String key;
    
    @Produces
    @ApplicationScoped
    public CosmosClient cosmosClient() {
        return new CosmosClientBuilder()
            .endpoint(endpoint)
            .key(key)
            .consistencyLevel(ConsistencyLevel.SESSION)
            .buildClient();
    }
}
```

**EmployeeRepository.java:**
```java
@ApplicationScoped
public class EmployeeRepository {
    
    @Inject
    CosmosClient cosmosClient;
    
    @ConfigProperty(name = "azure.cosmos.database")
    String databaseName;
    
    private static final String CONTAINER_NAME = "employees";
    
    public Employee create(Employee employee) {
        CosmosContainer container = getContainer();
        
        employee.setId(UUID.randomUUID().toString());
        employee.setCreatedDate(LocalDateTime.now());
        
        CosmosItemResponse<Employee> response = container.createItem(
            employee,
            new PartitionKey(employee.getUserId()),
            new CosmosItemRequestOptions()
        );
        
        return response.getItem();
    }
    
    public Employee findById(String id, String userId) {
        CosmosContainer container = getContainer();
        
        try {
            CosmosItemResponse<Employee> response = container.readItem(
                id,
                new PartitionKey(userId),
                Employee.class
            );
            return response.getItem();
        } catch (CosmosException e) {
            if (e.getStatusCode() == 404) {
                return null;
            }
            throw e;
        }
    }
    
    public List<Employee> findByUserId(String userId) {
        CosmosContainer container = getContainer();
        
        String query = "SELECT * FROM c WHERE c.userId = @userId";
        
        CosmosQueryRequestOptions options = new CosmosQueryRequestOptions();
        options.setPartitionKey(new PartitionKey(userId));
        
        return container.queryItems(
            query,
            options,
            Employee.class
        ).stream().collect(Collectors.toList());
    }
    
    private CosmosContainer getContainer() {
        return cosmosClient
            .getDatabase(databaseName)
            .getContainer(CONTAINER_NAME);
    }
}
```

### 📊 Comparación: Cosmos DB vs Alternativas

| Característica | Cosmos DB | MongoDB Atlas | DynamoDB | Azure SQL |
|----------------|-----------|---------------|----------|-----------|
| **Global distribution** | Nativo | Manual | Regional | Geo-replication manual |
| **SLA** | 99.999% | 99.99% | 99.99% | 99.99% |
| **Latencia P99** | <10ms | ~20ms | ~10ms | 20-50ms |
| **Consistency** | 5 niveles | Eventual/Strong | Eventual | Strong (ACID) |
| **APIs** | 6 APIs | MongoDB | Key-value | SQL |
| **Escalabilidad** | Ilimitada | Hasta 4TB/cluster | Ilimitada | Hasta 4TB |
| **Precio** | Alto | Medio | Medio | Bajo |
| **Serverless** | Sí | No | Sí | No |
| **Change stream** | Change Feed | Change Streams | Streams | Change Tracking |
| **Multi-cloud** | No | Sí | No | No |

---

Esta es la guía completa de Azure Cosmos DB. ¿Deseas que continúe con otro servicio? Aún quedan varios en la lista. 🚀