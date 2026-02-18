# 🔴 Azure Cache for Redis - Guía Completa

## 📖 Definición Detallada

Azure Cache for Redis es un **servicio de caché en memoria completamente administrado** basado en Redis (Remote Dictionary Server), un almacén de datos key-value de código abierto extremadamente rápido. Proporciona latencias de submilisegundo y alta throughput para mejorar el rendimiento de aplicaciones mediante el almacenamiento temporal de datos frecuentemente accedidos.

**Arquitectura de Redis:**

**Estructuras de datos soportadas:**
- **Strings:** Valores simples (texto, números, binarios) hasta 512 MB
- **Hashes:** Maps de campos string-valor (objetos)
- **Lists:** Listas enlazadas de strings (colas, stacks)
- **Sets:** Colecciones no ordenadas de strings únicos
- **Sorted Sets:** Sets ordenados por score numérico
- **Bitmaps:** Operaciones a nivel de bit sobre strings
- **HyperLogLogs:** Estructuras probabilísticas para contar elementos únicos
- **Geospatial indexes:** Coordenadas geográficas con búsqueda por radio
- **Streams:** Log estructurado append-only para mensajería

**Modos de despliegue:**
- **Basic:** Un solo nodo sin réplica (dev/test)
- **Standard:** Primario + réplica con auto-failover
- **Premium:** Clustering, persistencia, VNet injection
- **Enterprise:** Redis Enterprise con módulos (RediSearch, RedisJSON, etc.)
- **Enterprise Flash:** Usa SSD para datos fríos (costo-eficiente para grandes datasets)

**Características de Azure Cache for Redis:**
- **Completamente administrado:** Microsoft gestiona patching, backups, monitoring
- **Alta disponibilidad:** 99.9% SLA (Standard) hasta 99.999% (Enterprise con Geo-replication)
- **Escalabilidad vertical y horizontal:** Cambiar tier o agregar shards sin downtime
- **Seguridad:** SSL/TLS, VNet integration, Private Link, Azure AD authentication
- **Geo-replication:** Réplicas activas/pasivas en múltiples regiones
- **Persistencia:** RDB snapshots y AOF (Append-Only File) para recuperación
- **Clustering:** Hasta 10 shards para escalar throughput (Premium/Enterprise)
- **Observabilidad:** Azure Monitor metrics, slow log, Redis insights

### 🎯 Importancia

**Por qué Redis es crítico en arquitecturas modernas:**

1. **Performance extremo:** Latencia <1ms (vs 50-100ms en DB relacional)
2. **Reducción de carga en DB:** Cache evita queries repetitivos costosos
3. **Escalabilidad horizontal:** Distribuye carga entre múltiples shards
4. **Session storage:** Aplicaciones stateless con sesiones compartidas
5. **Real-time analytics:** Contadores, leaderboards, trending topics
6. **Rate limiting:** Control de acceso distribuido
7. **Message broker:** Pub/Sub para comunicación entre microservicios
8. **Distributed locks:** Coordinación en sistemas distribuidos
9. **Caching patterns:** Cache-aside, write-through, write-behind
10. **Time-series data:** Métricas temporales con expiración automática

### ✨ Características Principales

**1. Performance y Throughput:**
- **Latencia:** <1ms para operaciones GET/SET
- **Throughput:** Hasta 2 millones ops/seg (Enterprise E100)
- **In-memory storage:** Todos los datos en RAM
- **Single-threaded por defecto:** Operaciones atómicas garantizadas
- **Pipelining:** Batch de múltiples comandos para reducir roundtrips

**2. Alta Disponibilidad:**
- **Replicación asíncrona:** Primario → Réplica(s)
- **Auto-failover:** < 1 minuto en Standard/Premium
- **Zone redundancy:** Réplicas en diferentes Availability Zones
- **Geo-replication (Premium):** Réplica activa en otra región
- **99.999% SLA:** Con Enterprise + Geo-replication activa-activa

**3. Persistencia de Datos:**
- **RDB (Redis Database):** Snapshots periódicos en Azure Storage
  - Intervalo configurable: cada 15 min, 1 hora, 6 horas, 12 horas, 24 horas
  - Útil para backup y disaster recovery
- **AOF (Append-Only File):** Log de todas las escrituras
  - Fsync cada segundo o cada operación
  - Recuperación completa en caso de fallo
- **Sin persistencia:** Datos puramente en memoria (volatile)

**4. Clustering (Premium/Enterprise):**
- **Sharding automático:** Datos distribuidos en hasta 10 shards
- **Consistent hashing:** Claves distribuidas uniformemente
- **Hash tags:** Control de qué claves van al mismo shard `{user:123}:profile`
- **Multi-key operations:** MGET, MSET funcionan si keys están en mismo shard
- **Scale out sin downtime:** Agregar/quitar shards on-the-fly

**5. Seguridad:**
- **SSL/TLS obligatorio:** Puerto 6380 (no-SSL 6379 deshabilitado por defecto)
- **Access keys:** Primary y secondary para rotación sin downtime
- **Azure AD authentication (Enterprise):** RBAC con identidades managed
- **VNet integration (Premium/Enterprise):** Redis solo accesible desde VNet privada
- **Private Link:** Acceso privado sin VNet injection
- **Firewall rules:** Whitelist de IPs permitidas

**6. Eviction Policies:**
Cuando la memoria está llena, Redis elimina keys según la política:
- **noeviction:** Retorna error, no elimina nada (default)
- **allkeys-lru:** Elimina keys menos recientemente usadas (cualquier key)
- **volatile-lru:** Elimina keys con TTL, las menos recientemente usadas
- **allkeys-lfu:** Elimina keys menos frecuentemente usadas
- **volatile-lfu:** Elimina keys con TTL, las menos frecuentemente usadas
- **allkeys-random:** Elimina keys aleatorias
- **volatile-random:** Elimina keys con TTL aleatorias
- **volatile-ttl:** Elimina keys con TTL más cercano a expirar

**7. Redis Modules (Enterprise):**
- **RediSearch:** Full-text search, indexación secundaria, aggregations
- **RedisJSON:** Documentos JSON nativos con JSONPath
- **RedisTimeSeries:** Time-series optimizado para métricas
- **RedisBloom:** Bloom filters, cuckoo filters, count-min sketch
- **RedisGraph:** Base de datos de grafos (Cypher query language)
- **RedisAI:** Ejecución de modelos de ML/DL (TensorFlow, PyTorch)

**8. Comandos Útiles:**
```redis
# Strings
SET key value EX 3600          # Set con expiración de 1 hora
GET key
INCR counter                    # Incremento atómico
MGET key1 key2 key3            # Get múltiple

# Hashes (objetos)
HSET user:123 name "John" age 30
HGET user:123 name
HGETALL user:123
HINCRBY user:123 age 1

# Lists (colas)
LPUSH queue task1              # Push izquierda
RPUSH queue task2              # Push derecha
LPOP queue                     # Pop izquierda (FIFO con RPUSH+LPOP)
BRPOP queue 5                  # Blocking pop (espera hasta 5 seg)

# Sets (únicos)
SADD tags:post:1 redis azure cloud
SMEMBERS tags:post:1
SISMEMBER tags:post:1 redis    # Existe?
SINTER tags:post:1 tags:post:2 # Intersección

# Sorted Sets (leaderboards)
ZADD leaderboard 100 user1 200 user2 150 user3
ZRANGE leaderboard 0 -1 WITHSCORES
ZREVRANGE leaderboard 0 9      # Top 10

# TTL
EXPIRE key 3600                # Expira en 1 hora
TTL key                        # Tiempo restante
PERSIST key                    # Quitar expiración

# Pub/Sub
PUBLISH channel "mensaje"
SUBSCRIBE channel

# Transacciones
MULTI
SET key1 value1
INCR counter
EXEC

# Lua scripts (atómicos)
EVAL "return redis.call('GET', KEYS[1])" 1 mykey
```

### ✅ Ventajas

1. **Performance extremo:** 1000x más rápido que DB tradicional para reads
2. **Escalabilidad lineal:** Agregar shards aumenta throughput proporcionalmente
3. **Versatilidad:** Cache, session store, message broker, leaderboards, rate limiting
4. **Atomicidad:** Operaciones atómicas sin race conditions
5. **TTL automático:** Datos expiran sin gestión manual
6. **Estructuras de datos ricas:** No solo key-value, sino listas, sets, sorted sets
7. **Pub/Sub ligero:** Alternativa simple a message brokers pesados
8. **Lua scripting:** Lógica compleja ejecutada atómicamente en el servidor
9. **Open-source:** Portable entre clouds y on-premises
10. **Managed service:** Microsoft gestiona infraestructura, patching, monitoring

### ❌ Desventajas

1. **Costo de RAM:** Memoria es cara (~10x más que SSD), datasets grandes son costosos
2. **Volatilidad:** Sin persistencia, perder el nodo = perder datos (mitigar con RDB/AOF)
3. **Complejidad de clustering:** Multi-key operations no funcionan cross-shard
4. **Single-threaded:** Un comando lento bloquea todos los demás (usar comandos O(1))
5. **Límite de tamaño de key:** 512 MB máximo por key (no para BLOBs grandes)
6. **Cold start lento:** Cargar datos desde persistencia puede tardar minutos
7. **Eviction inesperada:** Datos pueden ser eliminados si memoria está llena
8. **No es base de datos:** No reemplaza DB relacional (sin ACID completo, sin queries complejos)
9. **Replicación asíncrona:** Posible pérdida de datos en failover (segundos de escrituras)
10. **Throttling:** Límites de bandwidth y IOPS por tier (caro escalar)

### 🎯 Cuándo Aplicarlo

**✅ USAR Azure Cache for Redis cuando:**

1. **Cache de base de datos:** Query results, objetos ORM, aggregations costosas
2. **Session management:** Aplicaciones web stateless con múltiples instancias
3. **Rate limiting distribuido:** Limitar requests por usuario/IP en microservicios
4. **Leaderboards y rankings:** Gaming, social media, e-commerce
5. **Real-time analytics:** Contadores, trending topics, dashboards en vivo
6. **Message queue ligera:** Pub/Sub para eventos no críticos
7. **Distributed locking:** Redlock algorithm para coordinación
8. **Full-page cache:** Cache de HTML completo para sitios con alto tráfico
9. **API response cache:** Cachear respuestas de APIs externas costosas
10. **Geospatial queries:** Búsqueda de ubicaciones cercanas (Uber, delivery apps)

**Ejemplos específicos:**
- **E-commerce:** Cache de catálogo de productos, carrito de compras en Redis
- **Social media:** Timeline feeds, contadores de likes/followers
- **Gaming:** Leaderboards en tiempo real, matchmaking
- **Fintech:** Rate limiting de APIs, cache de cotizaciones de bolsa
- **Media streaming:** Recomendaciones, trending videos
- **IoT:** Agregación de telemetría en tiempo real
- **Healthcare:** Cache de registros médicos para consultas rápidas

**❌ NO USAR Azure Cache for Redis cuando:**

1. **Datos críticos que no pueden perderse:** Usar DB relacional (Azure SQL, Cosmos)
2. **Datasets > 1.2 TB:** Redis no es almacenamiento primario (usar Cosmos DB, Synapse)
3. **Queries complejos con joins:** Redis no es DB relacional (usar SQL)
4. **Transacciones ACID distribuidas:** Redis no es ACID completo
5. **Full-text search avanzado:** Mejor Azure Cognitive Search (a menos que uses RediSearch)
6. **Almacenamiento de archivos:** BLOBs grandes → Azure Storage, no Redis
7. **Event streaming con replay:** Mejor Event Hubs o Kafka (Pub/Sub de Redis no persiste)
8. **Datos que raramente se acceden:** No justifica costo de RAM
9. **Budget muy limitado:** RAM es caro, evaluar alternativas más baratas
10. **Garantía de escritura durable:** Redis con AOF tiene latencia, no es instantáneo

### 📋 Situaciones Específicas

**Escenario 1: Blog de WordPress con bajo tráfico**
- ❌ Redis: Overhead innecesario
- ✅ Alternativa: Object cache de WordPress en disco

**Escenario 2: API de e-commerce con 10,000 RPM**
- ✅ Redis: Cache de productos, sesiones, carrito

**Escenario 3: Sistema bancario con transacciones críticas**
- ❌ Redis como DB principal: Usar Azure SQL + Redis solo para cache no crítico
- ✅ Redis: Cache de saldos (con TTL corto), rate limiting de API

**Escenario 4: Aplicación de chat en tiempo real**
- ✅ Redis: Pub/Sub para mensajes, Sorted Sets para historial reciente
- ⚠️ Para historial completo: Usar Cosmos DB + Redis para últimos 100 mensajes

**Escenario 5: Machine Learning inference server**
- ✅ Redis: Cache de predicciones de modelos, feature store
- ⚠️ Modelos grandes: Usar Azure ML model registry, no Redis

**Escenario 6: Sistema de inventario con 1 millón de SKUs**
- ✅ Redis Premium con clustering: Distributed cache de inventario
- ⚠️ Source of truth: Azure SQL, Redis es cache

**Escenario 7: Monitoreo de IoT con 100,000 sensores**
- ✅ Redis: Agregación de métricas en tiempo real, time-series
- ⚠️ Almacenamiento long-term: Time Series Insights o Azure Data Explorer

**Escenario 8: Sistema de autenticación con JWT**
- ✅ Redis: Blacklist de tokens revocados, session storage
- ⚠️ Para user database: Azure AD B2C, no Redis

### 🔧 Políticas y Mejores Prácticas

#### 1. Naming Conventions

```redis
# Usar prefijos jerárquicos con ":"
user:123:profile           # Objeto usuario
user:123:sessions          # Sesiones del usuario
product:456:inventory      # Inventario de producto
cache:api:users:list       # Cache de API

# Para clustering, usar hash tags {}
{user:123}:profile         # Misma partición
{user:123}:orders          # Misma partición
```

#### 2. TTL Strategy

```redis
# Siempre establecer TTL para evitar memoria llena
SET cache:product:123 <data> EX 3600      # 1 hora

# TTL dinámico según tipo de dato
SET cache:hot:data <data> EX 300          # 5 min para datos calientes
SET cache:cold:data <data> EX 86400       # 24 horas para datos fríos

# TTL para rate limiting
SET ratelimit:user:123 1 EX 60 NX         # 1 minuto ventana
INCR ratelimit:user:123
```

#### 3. Cache Patterns

**Cache-Aside (Lazy Loading):**
```java
public User getUser(Long id) {
    String key = "user:" + id;
    
    // 1. Buscar en cache
    String cached = redis.get(key);
    if (cached != null) {
        return deserialize(cached);
    }
    
    // 2. Si no está, buscar en DB
    User user = database.findById(id);
    
    // 3. Guardar en cache
    if (user != null) {
        redis.setex(key, 3600, serialize(user));
    }
    
    return user;
}
```

**Write-Through (escribir en cache y DB simultáneamente):**
```java
public void updateUser(User user) {
    String key = "user:" + user.getId();
    
    // 1. Actualizar DB
    database.save(user);
    
    // 2. Actualizar cache
    redis.setex(key, 3600, serialize(user));
}
```

**Write-Behind (escribir en cache, asíncrono a DB):**
```java
public void updateUser(User user) {
    String key = "user:" + user.getId();
    
    // 1. Actualizar cache inmediatamente
    redis.setex(key, 3600, serialize(user));
    
    // 2. Queue para actualizar DB asíncrono
    updateQueue.add(user);
}
```

#### 4. Connection Pooling

```yaml
# Spring Boot application.yml
spring:
  redis:
    host: myredis.redis.cache.windows.net
    port: 6380
    ssl: true
    password: ${REDIS_ACCESS_KEY}
    timeout: 2000
    lettuce:
      pool:
        max-active: 50      # Máximo de conexiones
        max-idle: 25        # Conexiones idle en pool
        min-idle: 10        # Mínimo de conexiones idle
        max-wait: 1000      # Tiempo de espera por conexión (ms)
      shutdown-timeout: 100
```

#### 5. Monitoring y Alertas

```bash
# Métricas críticas
- Cache Hit Ratio: > 80% (si es menor, revisar TTL o tamaño de cache)
- CPU: < 80% (Redis es single-threaded, >80% indica bottleneck)
- Memory: < 90% (riesgo de eviction)
- Connected Clients: Monitorear spikes anormales
- Commands/sec: Baseline para detectar anomalías
- Evicted Keys: > 0 indica memoria insuficiente

# Alertas
- CPU > 80% por 5 minutos → Escalar tier
- Memory > 90% → Aumentar tamaño o habilitar eviction
- Cache Hit < 70% → Revisar TTL o warming strategy
- Latency > 5ms → Investigar slow commands
```

#### 6. Sizing y Capacity Planning

```
# Fórmula para calcular memoria necesaria
Total Memory = (Avg Key Size + Avg Value Size) × Number of Keys × 1.2

# Ejemplo: 1 millón de users con perfil de 1 KB
Memory = (50 bytes + 1024 bytes) × 1,000,000 × 1.2 = 1.29 GB
→ Usar tier C1 (1 GB) o C2 (2.5 GB)

# Considerar overhead:
- Redis metadata: ~20% adicional
- Replicación: 2x memoria si tienes réplica
- Snapshots RDB: Hasta 2x memoria temporalmente durante snapshot
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear Azure Cache for Redis

**Opción A: Azure CLI (Standard tier)**
```bash
# Variables
RESOURCE_GROUP="rg-redis-prod"
LOCATION="eastus"
REDIS_NAME="redis-myapp-prod"
SKU="Standard"
SIZE="C1"  # C0=250MB, C1=1GB, C2=2.5GB, C3=6GB, C4=13GB, C5=26GB, C6=53GB

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear Redis Standard (con réplica)
az redis create \
  --resource-group $RESOURCE_GROUP \
  --name $REDIS_NAME \
  --location $LOCATION \
  --sku $SKU \
  --vm-size $SIZE \
  --enable-non-ssl-port false \
  --minimum-tls-version 1.2 \
  --redis-version 6

# Obtener access keys
az redis list-keys --resource-group $RESOURCE_GROUP --name $REDIS_NAME

# Obtener hostname
az redis show \
  --resource-group $RESOURCE_GROUP \
  --name $REDIS_NAME \
  --query hostName -o tsv
```

**Opción B: Premium tier con clustering**
```bash
# Crear Redis Premium con clustering
az redis create \
  --resource-group $RESOURCE_GROUP \
  --name redis-myapp-premium \
  --location $LOCATION \
  --sku Premium \
  --vm-size P1 \
  --shard-count 3 \
  --enable-non-ssl-port false \
  --minimum-tls-version 1.2 \
  --zones 1 2 3

# Habilitar persistencia RDB
az redis patch-schedule create \
  --resource-group $RESOURCE_GROUP \
  --name redis-myapp-premium \
  --schedule-entries '[{"dayOfWeek":"Everyday","startHourUtc":3,"maintenanceWindow":"PT5H"}]'

az redis update \
  --resource-group $RESOURCE_GROUP \
  --name redis-myapp-premium \
  --set redisConfiguration.rdb-backup-enabled=true \
  --set redisConfiguration.rdb-backup-frequency=60 \
  --set redisConfiguration.rdb-storage-connection-string="<storage-connection-string>"
```

**Opción C: Terraform**
```hcl
# variables.tf
variable "environment" {
  default = "prod"
}

variable "location" {
  default = "eastus"
}

# main.tf
resource "azurerm_resource_group" "rg" {
  name     = "rg-redis-${var.environment}"
  location = var.location
}

# Virtual Network para Premium tier
resource "azurerm_virtual_network" "vnet" {
  name                = "vnet-redis"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "redis_subnet" {
  name                 = "subnet-redis"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

# Storage Account para persistencia RDB
resource "azurerm_storage_account" "redis_backup" {
  name                     = "stredisbackup${var.environment}"
  resource_group_name      = azurerm_resource_group.rg.name
  location                 = azurerm_resource_group.rg.location
  account_tier             = "Standard"
  account_replication_type = "GRS"
}

# Redis Premium con todas las features
resource "azurerm_redis_cache" "redis" {
  name                = "redis-myapp-${var.environment}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  
  capacity            = 1              # P1
  family              = "P"            # Premium
  sku_name            = "Premium"
  
  enable_non_ssl_port = false
  minimum_tls_version = "1.2"
  
  shard_count         = 3              # 3 shards para clustering
  zones               = ["1", "2", "3"] # Zone redundancy
  
  # VNet injection
  subnet_id           = azurerm_subnet.redis_subnet.id
  
  # Persistencia RDB
  redis_configuration {
    enable_authentication           = true
    maxmemory_policy               = "allkeys-lru"
    maxmemory_reserved             = 10
    maxfragmentationmemory_reserved = 10
    
    # RDB backup
    rdb_backup_enabled             = true
    rdb_backup_frequency           = 60  # minutos
    rdb_backup_max_snapshot_count  = 1
    rdb_storage_connection_string  = azurerm_storage_account.redis_backup.primary_connection_string
    
    # AOF persistence (opcional)
    # aof_backup_enabled            = true
    # aof_storage_connection_string_0 = azurerm_storage_account.redis_backup.primary_connection_string
  }
  
  # Patch schedule (ventana de mantenimiento)
  patch_schedule {
    day_of_week    = "Sunday"
    start_hour_utc = 3
  }
  
  tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}

# Log Analytics para monitoring
resource "azurerm_log_analytics_workspace" "logs" {
  name                = "logs-redis-${var.environment}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}

resource "azurerm_monitor_diagnostic_setting" "redis_diagnostics" {
  name                       = "redis-diagnostics"
  target_resource_id         = azurerm_redis_cache.redis.id
  log_analytics_workspace_id = azurerm_log_analytics_workspace.logs.id

  enabled_log {
    category = "ConnectedClientList"
  }

  metric {
    category = "AllMetrics"
  }
}

# Private Endpoint (alternativa a VNet injection)
resource "azurerm_private_endpoint" "redis_endpoint" {
  name                = "pe-redis"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.pe_subnet.id

  private_service_connection {
    name                           = "psc-redis"
    private_connection_resource_id = azurerm_redis_cache.redis.id
    is_manual_connection           = false
    subresource_names              = ["redisCache"]
  }
}

# outputs.tf
output "redis_hostname" {
  value = azurerm_redis_cache.redis.hostname
}

output "redis_ssl_port" {
  value = azurerm_redis_cache.redis.ssl_port
}

output "redis_primary_key" {
  value     = azurerm_redis_cache.redis.primary_access_key
  sensitive = true
}
```

#### Paso 2: Configurar Firewall y Private Link

**Firewall rules:**
```bash
# Permitir IP específica
az redis firewall-rules create \
  --resource-group $RESOURCE_GROUP \
  --name $REDIS_NAME \
  --rule-name allow-office \
  --start-ip 203.0.113.10 \
  --end-ip 203.0.113.20

# Permitir rango de IPs
az redis firewall-rules create \
  --resource-group $RESOURCE_GROUP \
  --name $REDIS_NAME \
  --rule-name allow-app-subnet \
  --start-ip 10.0.0.0 \
  --end-ip 10.0.255.255
```

**Private Link:**
```bash
# Crear Private Endpoint (ver Terraform arriba para implementación completa)
# Beneficio: Redis accesible solo desde VNet, sin IP pública
```

#### Paso 3: Rotar Access Keys sin Downtime

```bash
# 1. Aplicación usa PRIMARY key

# 2. Regenerar SECONDARY key
az redis regenerate-key \
  --resource-group $RESOURCE_GROUP \
  --name $REDIS_NAME \
  --key-type secondary

# 3. Actualizar aplicación para usar SECONDARY key
# 4. Desplegar aplicación

# 5. Regenerar PRIMARY key (ahora no está en uso)
az redis regenerate-key \
  --resource-group $RESOURCE_GROUP \
  --name $REDIS_NAME \
  --key-type primary

# 6. Volver a PRIMARY key en siguiente ciclo
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
    
    <!-- Spring Data Redis -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    
    <!-- Lettuce (cliente Redis recomendado) -->
    <dependency>
        <groupId>io.lettuce</groupId>
        <artifactId>lettuce-core</artifactId>
    </dependency>
    
    <!-- Jackson para serialización JSON -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
    
    <!-- Para @Cacheable annotations -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-cache</artifactId>
    </dependency>
</dependencies>
```

#### 2. Configuración

**application.yml:**
```yaml
spring:
  application:
    name: employee-api
  
  # Redis configuration
  redis:
    host: redis-myapp-prod.redis.cache.windows.net
    port: 6380
    ssl: true
    password: ${REDIS_ACCESS_KEY}
    timeout: 2000ms
    
    lettuce:
      pool:
        max-active: 50
        max-idle: 25
        min-idle: 10
        max-wait: 1000ms
      shutdown-timeout: 100ms
  
  # Cache configuration
  cache:
    type: redis
    redis:
      time-to-live: 3600000  # 1 hora en milisegundos
      cache-null-values: false
      key-prefix: "cache:"
      use-key-prefix: true

# Logging
logging:
  level:
    io.lettuce: DEBUG
    org.springframework.data.redis: DEBUG
```

#### 3. Configuración de Redis

**RedisConfig.java:**
```java
@Configuration
@EnableCaching
public class RedisConfig {
    
    @Bean
    public RedisTemplate<String, Object> redisTemplate(
        RedisConnectionFactory connectionFactory
    ) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        
        // Serializers
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = 
            new Jackson2JsonRedisSerializer<>(Object.class);
        
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.activateDefaultTyping(
            LaissezFaireSubTypeValidator.instance,
            ObjectMapper.DefaultTyping.NON_FINAL,
            JsonTypeInfo.As.PROPERTY
        );
        jackson2JsonRedisSerializer.setObjectMapper(om);
        
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        
        // Key serializer
        template.setKeySerializer(stringRedisSerializer);
        template.setHashKeySerializer(stringRedisSerializer);
        
        // Value serializer
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        
        template.afterPropertiesSet();
        return template;
    }
    
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofHours(1))
            .serializeKeysWith(
                RedisSerializationContext.SerializationPair.fromSerializer(
                    new StringRedisSerializer()
                )
            )
            .serializeValuesWith(
                RedisSerializationContext.SerializationPair.fromSerializer(
                    new GenericJackson2JsonRedisSerializer()
                )
            )
            .disableCachingNullValues();
        
        // Configuraciones específicas por cache
        Map<String, RedisCacheConfiguration> cacheConfigurations = new HashMap<>();
        
        // Cache de productos: 5 minutos
        cacheConfigurations.put("products", 
            config.entryTtl(Duration.ofMinutes(5)));
        
        // Cache de usuarios: 1 hora
        cacheConfigurations.put("users", 
            config.entryTtl(Duration.ofHours(1)));
        
        // Cache de configuración: 24 horas
        cacheConfigurations.put("config", 
            config.entryTtl(Duration.ofHours(24)));
        
        return RedisCacheManager.builder(connectionFactory)
            .cacheDefaults(config)
            .withInitialCacheConfigurations(cacheConfigurations)
            .transactionAware()
            .build();
    }
}
```

#### 4. Uso Básico con RedisTemplate

**RedisService.java:**
```java
@Service
public class RedisService {
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    // ========== STRINGS ==========
    public void setValue(String key, Object value, long timeout, TimeUnit unit) {
        redisTemplate.opsForValue().set(key, value, timeout, unit);
    }
    
    public Object getValue(String key) {
        return redisTemplate.opsForValue().get(key);
    }
    
    public Long increment(String key) {
        return redisTemplate.opsForValue().increment(key);
    }
    
    // ========== HASHES ==========
    public void setHashValue(String key, String field, Object value) {
        redisTemplate.opsForHash().put(key, field, value);
    }
    
    public Object getHashValue(String key, String field) {
        return redisTemplate.opsForHash().get(key, field);
    }
    
    public Map<Object, Object> getHashAll(String key) {
        return redisTemplate.opsForHash().entries(key);
    }
    
    // ========== LISTS ==========
    public Long pushToList(String key, Object value) {
        return redisTemplate.opsForList().rightPush(key, value);
    }
    
    public Object popFromList(String key) {
        return redisTemplate.opsForList().leftPop(key);
    }
    
    public List<Object> getListRange(String key, long start, long end) {
        return redisTemplate.opsForList().range(key, start, end);
    }
    
    // ========== SETS ==========
    public Long addToSet(String key, Object... values) {
        return redisTemplate.opsForSet().add(key, values);
    }
    
    public Set<Object> getSetMembers(String key) {
        return redisTemplate.opsForSet().members(key);
    }
    
    public Boolean isMemberOfSet(String key, Object value) {
        return redisTemplate.opsForSet().isMember(key, value);
    }
    
    // ========== SORTED SETS ==========
    public Boolean addToSortedSet(String key, Object value, double score) {
        return redisTemplate.opsForZSet().add(key, value, score);
    }
    
    public Set<Object> getSortedSetRange(String key, long start, long end) {
        return redisTemplate.opsForZSet().range(key, start, end);
    }
    
    public Set<Object> getSortedSetReverseRange(String key, long start, long end) {
        return redisTemplate.opsForZSet().reverseRange(key, start, end);
    }
    
    // ========== TTL & EXPIRATION ==========
    public Boolean expire(String key, long timeout, TimeUnit unit) {
        return redisTemplate.expire(key, timeout, unit);
    }
    
    public Long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }
    
    public Boolean hasKey(String key) {
        return redisTemplate.hasKey(key);
    }
    
    public Boolean delete(String key) {
        return redisTemplate.delete(key);
    }
    
    // ========== BATCH OPERATIONS ==========
    public void batchSet(Map<String, Object> map, long timeout, TimeUnit unit) {
        redisTemplate.executePipelined(new SessionCallback<Object>() {
            @Override
            public Object execute(RedisOperations operations) throws DataAccessException {
                map.forEach((key, value) -> 
                    operations.opsForValue().set(key, value, timeout, unit)
                );
                return null;
            }
        });
    }
}
```

#### 5. Cache Aside Pattern

**EmployeeService.java:**
```java
@Service
public class EmployeeService {
    
    @Autowired
    private EmployeeRepository repository;
    
    @Autowired
    private RedisService redisService;
    
    private static final String CACHE_PREFIX = "employee:";
    private static final long CACHE_TTL = 3600; // 1 hora
    
    public Employee getEmployee(Long id) {
        String cacheKey = CACHE_PREFIX + id;
        
        // 1. Buscar en cache
        Employee cached = (Employee) redisService.getValue(cacheKey);
        if (cached != null) {
            log.info("Cache HIT for employee: {}", id);
            return cached;
        }
        
        // 2. Cache MISS - buscar en DB
        log.info("Cache MISS for employee: {}", id);
        Employee employee = repository.findById(id)
            .orElseThrow(() -> new NotFoundException("Employee not found"));
        
        // 3. Guardar en cache
        redisService.setValue(cacheKey, employee, CACHE_TTL, TimeUnit.SECONDS);
        
        return employee;
    }
    
    public Employee updateEmployee(Long id, Employee employee) {
        // 1. Actualizar DB
        Employee updated = repository.save(employee);
        
        // 2. Invalidar cache
        String cacheKey = CACHE_PREFIX + id;
        redisService.delete(cacheKey);
        
        // 3. Opcionalmente, actualizar cache (write-through)
        // redisService.setValue(cacheKey, updated, CACHE_TTL, TimeUnit.SECONDS);
        
        return updated;
    }
}
```

#### 6. Usando @Cacheable Annotations

**EmployeeServiceCacheable.java:**
```java
@Service
public class EmployeeServiceCacheable {
    
    @Autowired
    private EmployeeRepository repository;
    
    @Cacheable(value = "employees", key = "#id")
    public Employee getEmployee(Long id) {
        log.info("Fetching employee from DB: {}", id);
        return repository.findById(id)
            .orElseThrow(() -> new NotFoundException("Employee not found"));
    }
    
    @CachePut(value = "employees", key = "#employee.id")
    public Employee updateEmployee(Employee employee) {
        log.info("Updating employee: {}", employee.getId());
        return repository.save(employee);
    }
    
    @CacheEvict(value = "employees", key = "#id")
    public void deleteEmployee(Long id) {
        log.info("Deleting employee: {}", id);
        repository.deleteById(id);
    }
    
    @CacheEvict(value = "employees", allEntries = true)
    public void clearCache() {
        log.info("Clearing all employee cache");
    }
    
    // Cacheable con condition
    @Cacheable(value = "employees", key = "#id", unless = "#result == null")
    public Employee getEmployeeConditional(Long id) {
        return repository.findById(id).orElse(null);
    }
}
```

#### 7. Rate Limiting

**RateLimiterService.java:**
```java
@Service
public class RateLimiterService {
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    /**
     * Sliding window rate limiter
     * @param key identificador único (userId, IP, API key)
     * @param limit número máximo de requests
     * @param window ventana de tiempo en segundos
     * @return true si está permitido, false si excede el límite
     */
    public boolean isAllowed(String key, int limit, int window) {
        String redisKey = "ratelimit:" + key;
        long now = System.currentTimeMillis();
        long windowStart = now - (window * 1000L);
        
        // Usar Sorted Set con timestamp como score
        ZSetOperations<String, Object> zSet = redisTemplate.opsForZSet();
        
        // 1. Eliminar entradas fuera de la ventana
        zSet.removeRangeByScore(redisKey, 0, windowStart);
        
        // 2. Contar requests en la ventana actual
        Long count = zSet.count(redisKey, windowStart, now);
        
        if (count != null && count < limit) {
            // 3. Agregar request actual
            zSet.add(redisKey, UUID.randomUUID().toString(), now);
            
            // 4. Establecer expiración
            redisTemplate.expire(redisKey, window + 10, TimeUnit.SECONDS);
            
            return true;
        }
        
        return false;
    }
    
    /**
     * Token bucket rate limiter (más eficiente)
     */
    public boolean isAllowedTokenBucket(String key, int capacity, int refillRate) {
        String redisKey = "bucket:" + key;
        long now = System.currentTimeMillis() / 1000; // segundos
        
        String script = 
            "local tokens = redis.call('get', KEYS[1]) " +
            "local lastRefill = redis.call('get', KEYS[2]) " +
            "local capacity = tonumber(ARGV[1]) " +
            "local rate = tonumber(ARGV[2]) " +
            "local now = tonumber(ARGV[3]) " +
            "if tokens == false then " +
            "  tokens = capacity " +
            "  lastRefill = now " +
            "else " +
            "  tokens = tonumber(tokens) " +
            "  lastRefill = tonumber(lastRefill) " +
            "end " +
            "local elapsed = now - lastRefill " +
            "local newTokens = math.min(capacity, tokens + (elapsed * rate)) " +
            "if newTokens >= 1 then " +
            "  redis.call('set', KEYS[1], newTokens - 1) " +
            "  redis.call('set', KEYS[2], now) " +
            "  redis.call('expire', KEYS[1], 60) " +
            "  redis.call('expire', KEYS[2], 60) " +
            "  return 1 " +
            "else " +
            "  return 0 " +
            "end";
        
        DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
        redisScript.setScriptText(script);
        redisScript.setResultType(Long.class);
        
        Long result = redisTemplate.execute(
            redisScript,
            Arrays.asList(redisKey, redisKey + ":refill"),
            capacity, refillRate, now
        );
        
        return result != null && result == 1;
    }
}
```

**RateLimitInterceptor.java:**
```java
@Component
public class RateLimitInterceptor implements HandlerInterceptor {
    
    @Autowired
    private RateLimiterService rateLimiter;
    
    @Override
    public boolean preHandle(
        HttpServletRequest request,
        HttpServletResponse response,
        Object handler
    ) throws Exception {
        
        String userId = request.getHeader("X-User-ID");
        if (userId == null) {
            userId = request.getRemoteAddr(); // Fallback a IP
        }
        
        // 100 requests por minuto
        if (!rateLimiter.isAllowed(userId, 100, 60)) {
            response.setStatus(HttpStatus.TOO_MANY_REQUESTS.value());
            response.getWriter().write("Rate limit exceeded. Try again later.");
            return false;
        }
        
        return true;
    }
}
```

#### 8. Distributed Lock (Redlock)

**DistributedLockService.java:**
```java
@Service
public class DistributedLockService {
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    /**
     * Adquirir lock distribuido
     * @param lockKey nombre del lock
     * @param lockValue valor único (UUID)
     * @param expireTime tiempo de expiración en segundos
     * @return true si adquirió el lock, false si ya está tomado
     */
    public boolean acquireLock(String lockKey, String lockValue, long expireTime) {
        String redisKey = "lock:" + lockKey;
        
        Boolean success = redisTemplate.opsForValue()
            .setIfAbsent(redisKey, lockValue, expireTime, TimeUnit.SECONDS);
        
        return Boolean.TRUE.equals(success);
    }
    
    /**
     * Liberar lock (solo si el valor coincide)
     */
    public boolean releaseLock(String lockKey, String lockValue) {
        String redisKey = "lock:" + lockKey;
        
        String script = 
            "if redis.call('get', KEYS[1]) == ARGV[1] then " +
            "  return redis.call('del', KEYS[1]) " +
            "else " +
            "  return 0 " +
            "end";
        
        DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
        redisScript.setScriptText(script);
        redisScript.setResultType(Long.class);
        
        Long result = redisTemplate.execute(
            redisScript,
            Collections.singletonList(redisKey),
            lockValue
        );
        
        return result != null && result == 1;
    }
    
    /**
     * Ejecutar acción con lock automático
     */
    public <T> T executeWithLock(
        String lockKey,
        long expireTime,
        Supplier<T> action
    ) {
        String lockValue = UUID.randomUUID().toString();
        
        try {
            // Intentar adquirir lock (con retry)
            int maxRetries = 3;
            int retryDelayMs = 100;
            
            for (int i = 0; i < maxRetries; i++) {
                if (acquireLock(lockKey, lockValue, expireTime)) {
                    // Lock adquirido, ejecutar acción
                    return action.get();
                }
                Thread.sleep(retryDelayMs);
            }
            
            throw new RuntimeException("Could not acquire lock: " + lockKey);
            
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Interrupted while acquiring lock", e);
        } finally {
            // Liberar lock
            releaseLock(lockKey, lockValue);
        }
    }
}
```

**Uso del distributed lock:**
```java
@Service
public class PaymentService {
    
    @Autowired
    private DistributedLockService lockService;
    
    public void processPayment(String orderId, BigDecimal amount) {
        // Evitar procesamiento duplicado del mismo order
        lockService.executeWithLock(
            "payment:" + orderId,
            30, // 30 segundos
            () -> {
                // Lógica de procesamiento de pago
                // Solo un nodo puede ejecutar esto a la vez
                log.info("Processing payment for order: {}", orderId);
                // ...
                return null;
            }
        );
    }
}
```

### 💻 Ejemplo con Quarkus

**pom.xml:**
```xml
<dependencies>
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-redis-client</artifactId>
    </dependency>
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-cache</artifactId>
    </dependency>
</dependencies>
```

**application.properties:**
```properties
quarkus.redis.hosts=rediss://redis-myapp-prod.redis.cache.windows.net:6380
quarkus.redis.password=${REDIS_ACCESS_KEY}
quarkus.redis.ssl=true
quarkus.redis.max-pool-size=50
quarkus.redis.max-pool-waiting=30

# Cache
quarkus.cache.redis.ttl=1H
quarkus.cache.redis.key-prefix=cache:
```

**RedisService.java:**
```java
@ApplicationScoped
public class RedisService {
    
    @Inject
    RedisClient redisClient;
    
    public void set(String key, String value, Duration ttl) {
        redisClient.set(Arrays.asList(key, value, "EX", String.valueOf(ttl.getSeconds())));
    }
    
    public String get(String key) {
        Response response = redisClient.get(key);
        return response != null ? response.toString() : null;
    }
}
```

---

Esta es la guía completa de Azure Redis Cache. ¿Continúo con **Azure Cosmos DB**? 🌌