# 🌐 Azure App Service - Guía Completa

## 📖 Definición Detallada

Azure App Service es una **plataforma PaaS (Platform as a Service) completamente administrada** para construir, desplegar y escalar aplicaciones web, APIs REST, backends móviles y funciones web sin gestionar infraestructura. Soporta múltiples lenguajes (.NET, Java, Node.js, Python, PHP, Ruby) y frameworks (Spring Boot, Express, Django, Flask, etc.) en ambientes Windows y Linux.

**Componentes arquitectónicos:**

**1. App Service Plan (ASP):**
- **Definición:** Conjunto de recursos compute (CPU, RAM, storage) donde se ejecutan las apps
- **Pricing Tiers:**
  - **Free (F1):** Shared compute, 60 min CPU/day, 1 GB RAM, no custom domain
  - **Shared (D1):** Shared compute, 240 min CPU/day, 1 GB RAM, custom domains
  - **Basic (B1-B3):** Dedicated VMs, 1-4 cores, 1.75-7 GB RAM, SSL, manual scale
  - **Standard (S1-S3):** 1-4 cores, 1.75-7 GB RAM, auto-scale, staging slots, backups
  - **Premium (P1v2-P3v3):** 1-8 cores, 3.5-32 GB RAM, VNet integration, más slots
  - **Isolated (I1v2-I3v2):** App Service Environment (ASE), aislamiento completo
- **Características:**
  - Escala vertical (cambiar tier) y horizontal (más instancias)
  - Múltiples apps en el mismo plan (comparten recursos)
  - Linux o Windows (no mezclables en el mismo plan)

**2. Web Apps:**
- Aplicaciones web tradicionales (MVC, SPAs)
- APIs REST
- Backends para mobile apps
- Soporte para contenedores Docker

**3. Deployment Slots:**
- Entornos paralelos (staging, QA, blue/green)
- Swap entre slots sin downtime (warm-up automático)
- Traffic splitting para A/B testing y canary releases
- Configuraciones específicas por slot

**4. Continuous Deployment:**
- Integración con GitHub, Azure DevOps, Bitbucket, GitLab
- GitHub Actions y Azure Pipelines
- Docker Hub, Azure Container Registry
- Local Git, FTP, OneDrive

**5. Networking:**
- **Inbound:**
  - Public endpoint (default)
  - Access Restrictions (IP whitelisting, VNet integration)
  - Private Endpoint (Premium/Isolated)
- **Outbound:**
  - VNet Integration (conectar a recursos privados)
  - Hybrid Connections (on-premises sin VPN)
  - Service Endpoints

**6. Autenticación Integrada (Easy Auth):**
- Azure AD, Microsoft Account, Facebook, Google, Twitter
- Sin código en la aplicación
- Token store automático
- Claims en headers HTTP

**7. Managed Identities:**
- System-assigned: Identidad automática ligada al ciclo de vida del app
- User-assigned: Identidad reutilizable entre múltiples apps
- Acceso sin credenciales a Key Vault, Storage, SQL, etc.

### 🎯 Importancia

**Por qué App Service es crítico para desarrollo ágil:**

1. **Time-to-market:** Desplegar en minutos, no días
2. **Sin gestión de infraestructura:** Microsoft gestiona OS, runtime, patching
3. **Auto-scaling:** Escala automáticamente según carga (CPU, memoria, HTTP queue)
4. **Alta disponibilidad:** SLA 99.95%, multi-instance con load balancer incluido
5. **DevOps nativo:** CI/CD integrado, deployment slots para zero-downtime
6. **Seguridad integrada:** SSL/TLS automático, Easy Auth, Managed Identity
7. **Monitoreo incluido:** Application Insights sin instalación adicional
8. **Multi-lenguaje:** Stack unificado para .NET, Java, Node, Python, PHP
9. **Costo-efectivo:** Pay-per-use en tiers bajos, shared resources
10. **Enterprise-ready:** VNet integration, Private Endpoints, compliance

### ✨ Características Principales

**1. Runtime Support:**
- **.NET:** .NET 6, 7, 8 (LTS), .NET Framework 4.8
- **Java:** Java 8, 11, 17, 21 (con Tomcat, JBoss EAP)
- **Node.js:** 14 LTS, 16 LTS, 18 LTS, 20 LTS
- **Python:** 3.8, 3.9, 3.10, 3.11, 3.12
- **PHP:** 8.0, 8.1, 8.2
- **Ruby:** 2.7, 3.0, 3.1
- **Contenedores:** Docker (single/multi-container), Kubernetes (AKS integration)

**2. Deployment Methods:**
```bash
# Deployment Center (Portal)
- GitHub Actions (recomendado)
- Azure Pipelines
- Bitbucket Pipelines
- Local Git
- FTP/FTPS
- OneDrive/Dropbox

# Azure CLI
az webapp up                    # Deploy con auto-detect
az webapp deployment source config-zip    # ZIP deploy
az webapp deploy --src-path app.jar       # JAR deploy

# Maven Plugin
mvn azure-webapp:deploy

# Docker
docker push myregistry.azurecr.io/myapp:latest
az webapp config container set --docker-custom-image-name myapp:latest

# VS Code / Visual Studio
- Right-click → Publish
```

**3. Configuration Management:**
```yaml
# Application Settings (Environment Variables)
SPRING_PROFILES_ACTIVE: production
DATABASE_URL: @Microsoft.KeyVault(SecretUri=...)
JAVA_OPTS: -Xms512m -Xmx2048m

# Connection Strings
- SQL Server
- MySQL
- PostgreSQL
- Custom (Redis, MongoDB, etc.)

# General Settings
- Stack: Java 17
- Platform: Linux/Windows
- Always On: true (evita cold start)
- ARR Affinity: false (stateless apps)
- HTTP Version: 2.0
- Minimum TLS: 1.2
- FTP State: Disabled
```

**4. Auto-scaling Rules:**
```yaml
# Scale based on CPU
- Metric: CPU Percentage
- Condition: > 70%
- Action: Increase instance count by 1
- Cool down: 5 minutes

# Scale based on HTTP Queue Length
- Metric: HTTP Queue Length
- Condition: > 100
- Action: Increase instance count by 2
- Cool down: 5 minutes

# Scale based on Memory
- Metric: Memory Percentage
- Condition: > 80%
- Action: Increase instance count by 1

# Scale down
- Metric: CPU Percentage
- Condition: < 30%
- Action: Decrease instance count by 1
- Cool down: 10 minutes

# Time-based scaling
- Schedule: Every day 8 AM - 6 PM
- Instance count: 5
- Weekend: 2 instances
```

**5. Deployment Slots (Standard tier+):**
```
Production (100%)
  ├── staging (0%)     # Pre-production testing
  ├── qa (0%)          # QA testing
  └── canary (10%)     # Canary release (10% traffic)

# Swap workflow
1. Deploy to staging slot
2. Warm-up automático (App Service hace requests a staging)
3. Swap staging ↔ production (instantáneo, sin downtime)
4. Rollback si hay problemas (swap reverso)
```

**6. Custom Domains y SSL:**
```bash
# Agregar custom domain
az webapp config hostname add \
  --webapp-name myapp \
  --resource-group myResourceGroup \
  --hostname www.example.com

# SSL gratuito con App Service Managed Certificate
az webapp config ssl create \
  --resource-group myResourceGroup \
  --name myapp \
  --hostname www.example.com

# O subir certificado propio
az webapp config ssl upload \
  --resource-group myResourceGroup \
  --name myapp \
  --certificate-file cert.pfx \
  --certificate-password <password>

# Binding
az webapp config ssl bind \
  --resource-group myResourceGroup \
  --name myapp \
  --certificate-thumbprint <thumbprint> \
  --ssl-type SNI
```

**7. Monitoring y Diagnostics:**
```yaml
# Application Insights (auto-instrumentation)
- Request rates, response times, failure rates
- Dependencies (SQL, Redis, HTTP calls)
- Exceptions y stack traces
- Live metrics stream
- Application Map (topology)

# Diagnostic Logs
- Application logging (stdout/stderr)
- Web server logs (IIS/Apache)
- Detailed error messages
- Failed request tracing
- Deployment logs

# Log destinations
- File System (48 horas)
- Blob Storage (persistente)
- Log Analytics Workspace
- Event Hub (streaming)
```

**8. Backup y Restore:**
```bash
# Automatic backup (Standard tier+)
- Frequency: Hourly, daily, weekly
- Retention: Up to 30 days
- Includes: App content, configuration, databases
- Restore: To same or different app

# On-demand backup
az webapp config backup create \
  --resource-group myResourceGroup \
  --webapp-name myapp \
  --container-url <storage-sas-url> \
  --db-connection-string <connection-string>
```

### ✅ Ventajas

1. **Rapidez de despliegue:** De código a producción en minutos
2. **Sin administración de VMs:** Microsoft gestiona OS, patching, seguridad
3. **Built-in CI/CD:** GitHub Actions integrado, zero-config deployment
4. **Deployment slots:** Blue-green deployments sin infraestructura adicional
5. **Auto-scaling incorporado:** Escala horizontal automática por métricas
6. **SSL gratuito:** Certificados gestionados sin costo adicional
7. **Multi-stack:** Un servicio para .NET, Java, Node, Python, etc.
8. **Staging environments:** Testing en entornos idénticos a producción
9. **Easy Auth:** Autenticación sin código (Azure AD, Google, Facebook)
10. **Application Insights gratis:** Tier básico incluido sin costo adicional
11. **VNet integration:** Conectar a recursos privados sin complejidad
12. **Costo predecible:** Tiers claros, sin sorpresas en facturación
13. **Local development:** Mismo runtime en local que en cloud
14. **WebJobs:** Background tasks sin servicios adicionales
15. **Hybrid connections:** Conectar a on-premises sin VPN

### ❌ Desventajas

1. **Límites de recursos por tier:** B1 = 1.75 GB RAM, puede ser insuficiente
2. **Cold start en Free/Shared tiers:** App se apaga después de 20 min inactividad
3. **Sin control de VM:** No puedes instalar software custom en el OS
4. **Lock-in de Azure:** Migrar fuera de Azure requiere cambios (Easy Auth, App Settings)
5. **Límite de ejecución:** 230 segundos timeout (no para long-running processes)
6. **File system efímero en Linux:** Archivos escritos se pierden en restart
7. **Costo acumulado:** Tier Premium puede ser caro si siempre está activo
8. **Escalado limitado:** Max 30 instancias en Standard, 100 en Premium (vs AKS ilimitado)
9. **Dependencias compartidas:** Múltiples apps en mismo plan comparten recursos
10. **Windows legacy:** .NET Framework 4.x solo en Windows App Service
11. **No es serverless:** Pagas por las VMs 24/7, incluso sin tráfico (excepto Consumption)
12. **Troubleshooting limitado:** Sin acceso SSH completo en Windows
13. **Storage limitado:** 10 GB en Standard, 250 GB en Premium (vs discos de VM ilimitados)
14. **Reinicios programados:** Microsoft reinicia VMs semanalmente para patching

### 🎯 Cuándo Aplicarlo

**✅ USAR Azure App Service cuando:**

1. **Web apps tradicionales:** MVC, SPAs (React, Angular, Vue)
2. **APIs REST simples a medianas:** 10-50 endpoints, tráfico < 10,000 RPM
3. **Prototipado rápido:** MVPs, demos, PoCs
4. **Equipos pequeños:** Sin expertise en DevOps/Kubernetes
5. **Aplicaciones stateless:** Sin almacenamiento local persistente
6. **CI/CD requirement:** Necesitas despliegue automático desde Git
7. **Multi-environment:** Dev, staging, production con deployment slots
8. **Cost optimization:** Presupuesto limitado, compartes recursos entre apps
9. **Time-to-market crítico:** Lanzar en días, no semanas
10. **Legacy modernization:** Migrar .NET Framework apps desde on-premises
11. **Mobile backends:** APIs para apps iOS/Android con Easy Auth
12. **Microservicios ligeros:** 5-10 servicios independientes (antes de pasar a AKS)

**Ejemplos específicos:**
- Startup SaaS con 1,000 usuarios → App Service Standard
- E-commerce de tamaño medio → App Service Premium con auto-scaling
- Portal corporativo interno → App Service con VNet integration
- Blog o CMS (WordPress) → App Service con MySQL in App
- API de integración entre sistemas → App Service con Hybrid Connections
- Aplicación legacy .NET Framework → App Service Windows

**❌ NO USAR Azure App Service cuando:**

1. **Microservicios masivos:** 50+ servicios → Usar AKS
2. **Workloads stateful:** Apps que escriben archivos críticos → Usar VMs o AKS con Persistent Volumes
3. **Long-running processes:** Procesos > 230 segundos → Usar Azure Batch, VMs, o Container Apps
4. **GPU required:** Machine Learning inference → Usar AKS con GPU nodes
5. **Custom runtime:** Software específico no soportado → Usar VMs
6. **Costo extremadamente sensible:** Tráfico esporádico → Usar Azure Functions (serverless)
7. **Traffic ultra-alto:** Millones de RPM → Usar AKS con HPA
8. **Control total de OS:** Necesitas instalar software custom → Usar VMs
9. **Multi-cloud strategy:** Portabilidad crítica → Usar AKS (Kubernetes es portable)
10. **WebSockets persistentes a escala:** Usar Azure SignalR Service
11. **Background jobs complejos:** Usar Azure Functions con triggers
12. **Batch processing:** Grandes volúmenes de datos → Usar Azure Batch
13. **Streaming data:** Kafka, real-time processing → Usar Event Hubs + Stream Analytics

### 📋 Situaciones Específicas

**Escenario 1: Blog personal con 50 visitas/día**
- ✅ App Service Free tier: Perfecto para empezar, gratis
- ⚠️ Se apaga después de 20 min inactividad (cold start)

**Escenario 2: Startup SaaS B2B con tráfico creciente**
- ✅ App Service Standard S1 con auto-scaling
- ✅ Deployment slots para staging/production
- ✅ Application Insights para monitoreo

**Escenario 3: E-commerce con Black Friday traffic**
- ✅ App Service Premium P2v3 con auto-scale 2-20 instances
- ✅ CDN para static content
- ✅ Redis Cache para session/cart

**Escenario 4: Sistema bancario con alta seguridad**
- ✅ App Service Isolated (ASE) con Private Endpoints
- ✅ VNet integration para conectar a SQL Managed Instance
- ⚠️ Considerar on-premises si regulación lo requiere

**Escenario 5: Aplicación IoT con millones de mensajes/día**
- ❌ App Service: No diseñado para ingesta masiva
- ✅ Usar IoT Hub + Event Hubs + Azure Functions/Stream Analytics

**Escenario 6: Microservicios con 5 servicios simples**
- ✅ App Service: 5 web apps en un plan compartido
- ⚠️ Si creces a 20+ servicios, migrar a AKS

**Escenario 7: CRM corporativo con 500 usuarios internos**
- ✅ App Service Standard S1 con Easy Auth (Azure AD)
- ✅ VNet integration para SQL Server on-premises

**Escenario 8: Procesamiento de video (transcoding)**
- ❌ App Service: Timeout 230s, recursos limitados
- ✅ Usar Azure Media Services o Azure Batch

### 🔧 Políticas y Mejores Prácticas

#### 1. App Service Plan Sizing

**Elección de tier:**
```yaml
# Free/Shared (F1/D1)
- Uso: Dev/test, demos, blogs personales
- Limitaciones: Cold start, sin auto-scale, shared compute
- Costo: Gratis / $10/mes

# Basic (B1-B3)
- Uso: Pequeñas apps de producción, bajo tráfico
- Limitaciones: Sin deployment slots, sin auto-scale
- Costo: $55-220/mes

# Standard (S1-S3)
- Uso: Producción, tráfico medio, necesita staging
- Features: Deployment slots (5), auto-scale (10 instances), backups
- Costo: $70-280/mes

# Premium (P1v2-P3v3)
- Uso: Producción enterprise, alta disponibilidad
- Features: VNet integration, más slots (20), más instancias (30)
- Costo: $150-600/mes

# Isolated (I1v2-I3v2)
- Uso: Compliance estricto, aislamiento completo
- Features: App Service Environment, 100% privado
- Costo: $1,000-3,000/mes + ASE fee (~$1,000/mes)
```

**Fórmula de sizing:**
```
Concurrent Users = (Requests per Second × Average Response Time) / Users per Request
Instances Needed = (Peak Concurrent Users × Safety Factor) / Users per Instance

Ejemplo:
- Peak: 10,000 requests/second
- Avg response time: 200ms
- Concurrent users: 10,000 × 0.2 = 2,000
- Users per S1 instance: ~250 (benchmark)
- Instances: (2,000 × 1.5) / 250 = 12 instances
→ Usar Standard S1 con auto-scale 12-15 instances
```

#### 2. Always On y ARR Affinity

```yaml
# Always On (evita cold start)
alwaysOn: true  # RECOMENDADO en producción

# ARR Affinity (session stickiness)
clientAffinityEnabled: false  # RECOMENDADO para stateless apps

# Reasoning:
# - Always On: Mantiene app cargada, evita latencia en primer request
# - ARR Affinity OFF: Permite mejor load balancing, apps deben ser stateless
#   → Usa Redis/Cosmos para sesiones, no in-memory
```

#### 3. Deployment Best Practices

**Estrategia Blue-Green:**
```bash
# 1. Deploy a staging slot
az webapp deployment source config-zip \
  --resource-group myRG \
  --name myapp \
  --slot staging \
  --src target/myapp.jar

# 2. Validar staging
curl https://myapp-staging.azurewebsites.net/health

# 3. Swap con warm-up automático
az webapp deployment slot swap \
  --resource-group myRG \
  --name myapp \
  --slot staging \
  --target-slot production

# 4. Rollback si es necesario (swap reverso)
az webapp deployment slot swap \
  --resource-group myRG \
  --name myapp \
  --slot production \
  --target-slot staging
```

**Canary Release (traffic splitting):**
```bash
# 90% production, 10% canary
az webapp traffic-routing set \
  --resource-group myRG \
  --name myapp \
  --distribution staging=10

# Monitorear métricas, incrementar gradualmente
az webapp traffic-routing set \
  --resource-group myRG \
  --name myapp \
  --distribution staging=50

# Swap completo cuando estable
az webapp deployment slot swap ...
```

#### 4. Configuración por Slot

```yaml
# Configuraciones que NO se intercambian (slot-specific)
- Publishing endpoints
- Custom domain names
- SSL certificates and bindings
- Scale settings
- WebJobs schedulers
- IP restrictions
- Always On
- Hybrid connections

# Configuraciones que SÍ se intercambian
- General settings (framework version, 32/64-bit, web sockets)
- App settings (excepto si marcas como "slot setting")
- Connection strings (excepto si marcas como "slot setting")
- Handler mappings
- Public certificates
- WebJobs content
```

**Marcar app setting como slot-specific:**
```bash
az webapp config appsettings set \
  --resource-group myRG \
  --name myapp \
  --slot staging \
  --settings \
    "DATABASE_URL=<staging-db-url>" \
    --slot-settings DATABASE_URL
```

#### 5. Security Hardening

```yaml
# TLS/SSL
minTlsVersion: 1.2              # Mínimo TLS 1.2
httpsOnly: true                 # Force HTTPS
ftpsState: Disabled            # Deshabilitar FTP

# Access Restrictions
ipSecurityRestrictions:
  - ipAddress: 203.0.113.0/24
    action: Allow
    priority: 100
    name: "Office IP"
  - vnetSubnetResourceId: /subscriptions/.../subnets/app-subnet
    action: Allow
    priority: 200
    name: "VNet Access"
  - ipAddress: 0.0.0.0/0
    action: Deny
    priority: 2147483647
    name: "Deny all"

# Custom headers
customHeaders:
  - X-Frame-Options: DENY
  - X-Content-Type-Options: nosniff
  - Strict-Transport-Security: max-age=31536000; includeSubDomains
  - Content-Security-Policy: default-src 'self'
```

#### 6. Monitoring y Alerting

```yaml
# Application Insights
- Auto-instrumentation: Habilitado
- Sampling: 100% en dev, 10% en producción (reduce costos)
- Profiler: Habilitado (detecta bottlenecks)
- Snapshot debugger: Habilitado (debugging en producción)

# Alertas críticas
alerts:
  - Metric: HTTP 5xx errors
    Condition: > 10 in 5 minutes
    Action: Email + SMS

  - Metric: Response time P95
    Condition: > 1000ms for 5 minutes
    Action: Auto-scale + Email

  - Metric: CPU Percentage
    Condition: > 80% for 10 minutes
    Action: Auto-scale

  - Metric: Memory Percentage
    Condition: > 85% for 10 minutes
    Action: Email (review app/plan size)

  - Metric: Health check failures
    Condition: > 3 consecutive failures
    Action: Restart app + Email

# Health checks
healthCheck:
  path: /health
  interval: 60  # seconds
  timeout: 30
  unhealthy-threshold: 3
```

#### 7. Performance Optimization

```yaml
# HTTP/2
http20Enabled: true

# Compression
dynamicCompression: true
staticCompression: true

# Connection pooling (DB)
# Para SQL Server
connectionString: "Server=...;Max Pool Size=100;Min Pool Size=10;Connect Timeout=30"

# Para Java
JAVA_OPTS: "-Xms512m -Xmx2048m -XX:+UseG1GC -XX:MaxGCPauseMillis=200"

# CDN para static assets
- Azure CDN / Front Door
- Cache-Control headers: max-age=31536000 for immutable assets

# Warm-up (custom domain warming)
applicationInitialization:
  - path: /api/warmup
    delay: 30000  # 30 seconds

# Output caching
outputCache:
  enabled: true
  duration: 300  # 5 minutes for GET requests
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear App Service Plan y Web App

**Opción A: Azure CLI**
```bash
# Variables
RESOURCE_GROUP="rg-appservice-prod"
LOCATION="eastus"
PLAN_NAME="asp-myapp-prod"
APP_NAME="myapp-prod-001"  # Globalmente único
RUNTIME="JAVA:17-java17"   # O "NODE:18-lts", "PYTHON:3.11", "DOTNET:7.0"

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear App Service Plan (Linux, Standard S1)
az appservice plan create \
  --name $PLAN_NAME \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --is-linux \
  --sku S1

# Crear Web App
az webapp create \
  --resource-group $RESOURCE_GROUP \
  --plan $PLAN_NAME \
  --name $APP_NAME \
  --runtime $RUNTIME

# Configurar settings
az webapp config set \
  --resource-group $RESOURCE_GROUP \
  --name $APP_NAME \
  --always-on true \
  --http20-enabled true \
  --min-tls-version 1.2 \
  --ftps-state Disabled \
  --use-32bit-worker-process false

# Configurar app settings
az webapp config appsettings set \
  --resource-group $RESOURCE_GROUP \
  --name $APP_NAME \
  --settings \
    SPRING_PROFILES_ACTIVE=production \
    DATABASE_URL=@Microsoft.KeyVault(SecretUri=https://mykv.vault.azure.net/secrets/db-url) \
    JAVA_OPTS="-Xms512m -Xmx2048m"

# Habilitar Managed Identity
az webapp identity assign \
  --resource-group $RESOURCE_GROUP \
  --name $APP_NAME

# Obtener URL
az webapp show \
  --resource-group $RESOURCE_GROUP \
  --name $APP_NAME \
  --query defaultHostName -o tsv
```

**Opción B: Terraform**
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
  name     = "rg-appservice-${var.environment}"
  location = var.location
}

resource "azurerm_service_plan" "plan" {
  name                = "asp-myapp-${var.environment}"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  os_type             = "Linux"
  sku_name            = "S1"

  tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}

resource "azurerm_linux_web_app" "app" {
  name                = "myapp-${var.environment}-001"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_service_plan.plan.location
  service_plan_id     = azurerm_service_plan.plan.id

  site_config {
    always_on        = true
    http2_enabled    = true
    minimum_tls_version = "1.2"
    ftps_state       = "Disabled"
    
    application_stack {
      java_server         = "JAVA"
      java_server_version = "17"
      java_version        = "17"
    }

    health_check_path = "/actuator/health"
    health_check_eviction_time_in_min = 5

    # CORS
    cors {
      allowed_origins = ["https://example.com"]
      support_credentials = true
    }
  }

  app_settings = {
    "SPRING_PROFILES_ACTIVE"              = "production"
    "JAVA_OPTS"                           = "-Xms512m -Xmx2048m"
    "WEBSITE_RUN_FROM_PACKAGE"            = "1"
    "APPINSIGHTS_INSTRUMENTATIONKEY"      = azurerm_application_insights.insights.instrumentation_key
    "ApplicationInsightsAgent_EXTENSION_VERSION" = "~3"
  }

  connection_string {
    name  = "DefaultConnection"
    type  = "SQLAzure"
    value = "@Microsoft.KeyVault(SecretUri=${azurerm_key_vault_secret.db_connection.id})"
  }

  identity {
    type = "SystemAssigned"
  }

  logs {
    detailed_error_messages = true
    failed_request_tracing  = true

    application_logs {
      file_system_level = "Information"
    }

    http_logs {
      file_system {
        retention_in_days = 7
        retention_in_mb   = 35
      }
    }
  }

  https_only = true

  tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}

# Deployment slot (staging)
resource "azurerm_linux_web_app_slot" "staging" {
  name           = "staging"
  app_service_id = azurerm_linux_web_app.app.id

  site_config {
    always_on     = true
    http2_enabled = true

    application_stack {
      java_server         = "JAVA"
      java_server_version = "17"
      java_version        = "17"
    }
  }

  app_settings = {
    "SPRING_PROFILES_ACTIVE" = "staging"
    "JAVA_OPTS"              = "-Xms512m -Xmx1024m"
  }
}

# Application Insights
resource "azurerm_application_insights" "insights" {
  name                = "appi-myapp-${var.environment}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  application_type    = "java"
  retention_in_days   = 90

  tags = {
    Environment = var.environment
  }
}

# Auto-scale settings
resource "azurerm_monitor_autoscale_setting" "autoscale" {
  name                = "autoscale-myapp"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  target_resource_id  = azurerm_service_plan.plan.id

  profile {
    name = "default"

    capacity {
      default = 2
      minimum = 2
      maximum = 10
    }

    rule {
      metric_trigger {
        metric_name        = "CpuPercentage"
        metric_resource_id = azurerm_service_plan.plan.id
        time_grain         = "PT1M"
        statistic          = "Average"
        time_window        = "PT5M"
        time_aggregation   = "Average"
        operator           = "GreaterThan"
        threshold          = 70
      }

      scale_action {
        direction = "Increase"
        type      = "ChangeCount"
        value     = "1"
        cooldown  = "PT5M"
      }
    }

    rule {
      metric_trigger {
        metric_name        = "CpuPercentage"
        metric_resource_id = azurerm_service_plan.plan.id
        time_grain         = "PT1M"
        statistic          = "Average"
        time_window        = "PT10M"
        time_aggregation   = "Average"
        operator           = "LessThan"
        threshold          = 30
      }

      scale_action {
        direction = "Decrease"
        type      = "ChangeCount"
        value     = "1"
        cooldown  = "PT10M"
      }
    }
  }

  # Scale based on schedule (business hours)
  profile {
    name = "business-hours"

    capacity {
      default = 5
      minimum = 5
      maximum = 10
    }

    recurrence {
      timezone = "Eastern Standard Time"
      days     = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
      hours    = [8]
      minutes  = [0]
    }
  }

  # Scale down after hours
  profile {
    name = "after-hours"

    capacity {
      default = 2
      minimum = 2
      maximum = 5
    }

    recurrence {
      timezone = "Eastern Standard Time"
      days     = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
      hours    = [18]
      minutes  = [0]
    }
  }
}

# Custom domain y SSL
resource "azurerm_app_service_custom_hostname_binding" "custom_domain" {
  hostname            = "api.example.com"
  app_service_name    = azurerm_linux_web_app.app.name
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_app_service_managed_certificate" "cert" {
  custom_hostname_binding_id = azurerm_app_service_custom_hostname_binding.custom_domain.id
}

resource "azurerm_app_service_certificate_binding" "cert_binding" {
  hostname_binding_id = azurerm_app_service_custom_hostname_binding.custom_domain.id
  certificate_id      = azurerm_app_service_managed_certificate.cert.id
  ssl_state           = "SniEnabled"
}

# outputs.tf
output "app_url" {
  value = "https://${azurerm_linux_web_app.app.default_hostname}"
}

output "app_name" {
  value = azurerm_linux_web_app.app.name
}

output "staging_url" {
  value = "https://${azurerm_linux_web_app.app.name}-staging.azurewebsites.net"
}
```

#### Paso 2: Deploy desde Spring Boot

**Maven Plugin (azure-webapp-maven-plugin):**

**pom.xml:**
```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>2.12.0</version>
            <configuration>
                <subscriptionId>your-subscription-id</subscriptionId>
                <resourceGroup>rg-appservice-prod</resourceGroup>
                <appName>myapp-prod-001</appName>
                <region>eastus</region>
                <pricingTier>S1</pricingTier>
                <runtime>
                    <os>Linux</os>
                    <javaVersion>Java 17</javaVersion>
                    <webContainer>Java SE</webContainer>
                </runtime>
                <deployment>
                    <resources>
                        <resource>
                            <directory>${project.basedir}/target</directory>
                            <includes>
                                <include>*.jar</include>
                            </includes>
                        </resource>
                    </resources>
                </deployment>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Deploy:**
```bash
# Build y deploy
mvn clean package azure-webapp:deploy

# Deploy a staging slot
mvn azure-webapp:deploy -DdeploymentSlotName=staging
```

#### Paso 3: CI/CD con GitHub Actions

**.github/workflows/deploy.yml:**
```yaml
name: Deploy to Azure App Service

on:
  push:
    branches: [main]
  workflow_dispatch:

env:
  AZURE_WEBAPP_NAME: myapp-prod-001
  JAVA_VERSION: '17'

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up JDK ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v4
      with:
        java-version: ${{ env.JAVA_VERSION }}
        distribution: 'temurin'
        cache: maven
    
    - name: Build with Maven
      run: mvn clean package -DskipTests
    
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Deploy to staging slot
    - name: Deploy to Staging
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        slot-name: staging
        package: target/*.jar
    
    # Warmup staging
    - name: Warmup Staging
      run: |
        curl https://${{ env.AZURE_WEBAPP_NAME }}-staging.azurewebsites.net/actuator/health
        sleep 30
    
    # Smoke tests
    - name: Run Smoke Tests
      run: |
        ./scripts/smoke-tests.sh https://${{ env.AZURE_WEBAPP_NAME }}-staging.azurewebsites.net
    
    # Swap staging to production
    - name: Swap Staging to Production
      run: |
        az webapp deployment slot swap \
          --resource-group rg-appservice-prod \
          --name ${{ env.AZURE_WEBAPP_NAME }} \
          --slot staging \
          --target-slot production
```

### 💻 Ejemplo Spring Boot Completo

**application.yml:**
```yaml
spring:
  application:
    name: employee-api
  
  # Profile-specific config
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:local}
  
  # Datasource (connection string desde App Settings)
  datasource:
    url: ${DATABASE_URL}
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000

# Actuator para health checks
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      probes:
        enabled: true
      show-details: always
  
  # Application Insights
  metrics:
    export:
      azuremonitor:
        enabled: true

# Server config
server:
  port: 8080
  shutdown: graceful
  tomcat:
    threads:
      max: 200
      min-spare: 10

# Logging para App Service
logging:
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"
  level:
    root: INFO
    com.empresa: DEBUG
```

**Dockerfile (opcional, para custom container):**
```dockerfile
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Non-root user
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

COPY target/*.jar app.jar

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s \
  CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 💻 Ejemplo Quarkus

**application.properties:**
```properties
quarkus.application.name=employee-api
quarkus.http.port=8080

# Datasource
quarkus.datasource.jdbc.url=${DATABASE_URL}
quarkus.datasource.jdbc.max-size=20

# Health checks
quarkus.smallrye-health.root-path=/health

# Azure Application Insights
quarkus.application-insights.connection-string=${APPINSIGHTS_CONNECTION_STRING}
```

---

Esta es la guía completa de Azure App Service. ¿Continúo con **Azure Functions**? ⚡