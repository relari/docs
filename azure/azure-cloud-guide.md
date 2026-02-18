# ☁️ Microsoft Azure: Publicación y Consumo de Servicios en la Nube

## 📋 Índice
1. [¿Qué es Azure?](#que-es-azure)
2. [Arquitectura de Azure](#arquitectura)
3. [Componentes Principales](#componentes)
4. [Cómo una Empresa Publica Servicios](#publicacion)
5. [Cómo Terceros Consumen Servicios](#consumo)
6. [Casos de Uso Real](#casos-uso)
7. [Seguridad y Gobernanza](#seguridad)

---

## 1. ¿Qué es Azure? {#que-es-azure}

**Microsoft Azure** es una plataforma de computación en la nube que proporciona servicios de infraestructura (IaaS), plataforma (PaaS) y software (SaaS).

### Características Principales

| Característica | Descripción |
|----------------|-------------|
| **Global** | 60+ regiones en todo el mundo |
| **Escalable** | Crece según demanda |
| **Pago por uso** | Solo pagas lo que usas |
| **Alta disponibilidad** | SLA del 99.9% - 99.99% |
| **Seguridad** | Cumplimiento normativo (ISO, SOC, GDPR) |
| **Híbrido** | Integración con on-premise |

### Modelos de Servicio

```
┌─────────────────────────────────────────────────┐
│                    SaaS                         │
│  (Microsoft 365, Dynamics 365)                  │
│  Empresa usa apps listas                        │
├─────────────────────────────────────────────────┤
│                    PaaS                         │
│  (App Service, Azure Functions, SQL Database)   │
│  Empresa publica sus apps sin gestionar infra  │
├─────────────────────────────────────────────────┤
│                    IaaS                         │
│  (Virtual Machines, Storage, Network)           │
│  Empresa gestiona VMs, redes, storage          │
└─────────────────────────────────────────────────┘
```

---

## 2. Arquitectura de Azure {#arquitectura}

### Jerarquía Organizacional

```
Azure Active Directory (AAD)
    │
    └── Tenant (Inquilino)
        └── Subscriptions (Suscripciones)
            └── Resource Groups (Grupos de Recursos)
                └── Resources (Recursos)
                    ├── Virtual Machines
                    ├── App Services
                    ├── Databases
                    ├── Storage Accounts
                    └── APIs
```

### Componentes Clave

**1. Azure Active Directory (AAD):**
- Sistema de identidad y acceso
- Gestión de usuarios y permisos
- Single Sign-On (SSO)

**2. Subscription (Suscripción):**
- Contenedor de facturación
- Límites de recursos
- Control de costos

**3. Resource Group:**
- Contenedor lógico de recursos
- Ciclo de vida común
- Control de acceso compartido

**4. Region:**
- Ubicación geográfica de datacenter
- Baja latencia
- Cumplimiento normativo local

---

## 3. Componentes Principales de Azure {#componentes}

### 🖥️ Compute (Cómputo)

#### Azure Virtual Machines (VMs)
- Máquinas virtuales Windows/Linux
- Control total del sistema operativo
- **Uso:** Aplicaciones legacy, alta personalización

#### Azure App Service
- PaaS para aplicaciones web
- Soporta .NET, Java, Node.js, Python, PHP
- **Uso:** APIs REST, aplicaciones web

#### Azure Functions
- Serverless, sin gestión de infraestructura
- Pago por ejecución
- **Uso:** Microservicios, procesamiento de eventos

#### Azure Kubernetes Service (AKS)
- Orquestación de contenedores
- Kubernetes gestionado
- **Uso:** Microservicios containerizados

#### Azure Container Instances (ACI)
- Contenedores sin orquestación
- Inicio rápido
- **Uso:** Jobs, tareas batch

---

### 💾 Storage (Almacenamiento)

#### Azure Blob Storage
- Almacenamiento de objetos (archivos)
- Hot, Cool, Archive tiers
- **Uso:** Imágenes, videos, backups

#### Azure Files
- Sistema de archivos compartido (SMB/NFS)
- Acceso desde múltiples VMs
- **Uso:** Compartir archivos entre aplicaciones

#### Azure Queue Storage
- Colas de mensajes
- Comunicación asíncrona
- **Uso:** Desacoplar componentes

#### Azure Disk Storage
- Discos para VMs
- SSD Premium o HDD Standard
- **Uso:** Storage persistente para VMs

---

### 🗄️ Databases (Bases de Datos)

#### Azure SQL Database
- SQL Server gestionado (PaaS)
- Alta disponibilidad automática
- **Uso:** Apps empresariales relacionales

#### Azure Cosmos DB
- Base de datos NoSQL global
- Multi-modelo (document, key-value, graph)
- **Uso:** Apps distribuidas globalmente

#### Azure Database for PostgreSQL/MySQL
- PostgreSQL/MySQL gestionado
- Compatible con apps open-source
- **Uso:** Migración de apps existentes

#### Azure Redis Cache
- Caché en memoria
- Baja latencia
- **Uso:** Sesiones, caché de datos

---

### 🌐 Networking (Redes)

#### Azure Virtual Network (VNet)
- Red privada en Azure
- Aislamiento de recursos
- **Uso:** Conectar recursos de forma segura

#### Azure Load Balancer
- Distribución de tráfico
- Alta disponibilidad
- **Uso:** Balancear entre VMs

#### Azure Application Gateway
- Load balancer de capa 7 (HTTP/HTTPS)
- WAF integrado
- **Uso:** Balancear apps web

#### Azure Front Door
- CDN global + Load balancer
- Aceleración de contenido
- **Uso:** Apps distribuidas globalmente

#### Azure API Management (APIM)
- Gateway de APIs
- Seguridad, throttling, analytics
- **Uso:** Publicar y gestionar APIs

---

### 🔐 Security & Identity (Seguridad)

#### Azure Active Directory (AAD)
- Identidad como servicio
- OAuth 2.0, OpenID Connect
- **Uso:** Autenticación de usuarios

#### Azure Key Vault
- Gestión de secretos, certificados, claves
- HSM (Hardware Security Module)
- **Uso:** Almacenar credenciales de forma segura

#### Azure Security Center
- Monitoreo de seguridad
- Recomendaciones
- **Uso:** Compliance y protección

---

### 📊 Monitoring & Management (Monitoreo)

#### Azure Monitor
- Monitoreo de recursos
- Métricas, logs, alertas
- **Uso:** Observabilidad

#### Azure Application Insights
- APM (Application Performance Monitoring)
- Tracing distribuido
- **Uso:** Monitorear apps en producción

#### Azure Log Analytics
- Centralización de logs
- Consultas KQL
- **Uso:** Análisis de logs

---

### 🔄 Integration (Integración)

#### Azure Service Bus
- Mensajería empresarial
- Pub/Sub, Queue
- **Uso:** Comunicación entre servicios

#### Azure Event Grid
- Enrutamiento de eventos
- Arquitectura event-driven
- **Uso:** Reaccionar a eventos

#### Azure Logic Apps
- Workflows sin código
- Integraciones con SaaS
- **Uso:** Automatización de procesos

---

## 4. Cómo una Empresa Publica Servicios en Azure {#publicacion}

### Escenario: Empresa "TechCorp" publica API de Empleados

**Objetivo:** Publicar una API REST para que clientes externos consuman datos de empleados.

### Paso 1: Arquitectura de la Solución

```
┌──────────────────────────────────────────────────┐
│              Internet                            │
└──────────────┬───────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────────┐
│  Azure Front Door (CDN + SSL)                    │
│  - Terminación SSL                               │
│  - Cache global                                  │
│  - DDoS protection                               │
└──────────────┬───────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────────┐
│  Azure API Management (APIM)                     │
│  - Gateway                                       │
│  - Autenticación (API Keys, OAuth)              │
│  - Rate Limiting                                 │
│  - Analytics                                     │
│  - Versionado de API                             │
└──────────────┬───────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────────┐
│  Azure App Service / AKS                         │
│  - API REST (Spring Boot)                        │
│  - Multiple instances (escala horizontal)        │
│  - Health checks                                 │
└──────────────┬───────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────────┐
│  Azure SQL Database                              │
│  - Datos de empleados                            │
│  - Backups automáticos                           │
│  - Geo-replication                               │
└──────────────────────────────────────────────────┘
```

### Paso 2: Despliegue de la API

#### 2.1 Crear Resource Group
```bash
# Crear grupo de recursos
az group create \
  --name rg-techcorp-api \
  --location eastus
```

#### 2.2 Desplegar Base de Datos
```bash
# Crear SQL Server
az sql server create \
  --name techcorp-sql-server \
  --resource-group rg-techcorp-api \
  --location eastus \
  --admin-user sqladmin \
  --admin-password <password>

# Crear Base de Datos
az sql db create \
  --resource-group rg-techcorp-api \
  --server techcorp-sql-server \
  --name employee-db \
  --service-objective S0
```

#### 2.3 Desplegar App Service
```bash
# Crear App Service Plan
az appservice plan create \
  --name techcorp-plan \
  --resource-group rg-techcorp-api \
  --sku P1V2 \
  --is-linux

# Crear Web App
az webapp create \
  --resource-group rg-techcorp-api \
  --plan techcorp-plan \
  --name techcorp-employee-api \
  --runtime "JAVA:17-java17"

# Configurar connection string
az webapp config connection-string set \
  --resource-group rg-techcorp-api \
  --name techcorp-employee-api \
  --settings DB_CONNECTION="jdbc:sqlserver://techcorp-sql-server.database.windows.net:1433;database=employee-db" \
  --connection-string-type SQLAzure
```

#### 2.4 Configurar Azure API Management
```bash
# Crear API Management
az apim create \
  --resource-group rg-techcorp-api \
  --name techcorp-apim \
  --location eastus \
  --publisher-name "TechCorp" \
  --publisher-email "api@techcorp.com"

# Importar API
az apim api import \
  --resource-group rg-techcorp-api \
  --service-name techcorp-apim \
  --path /employees \
  --api-id employee-api \
  --specification-format OpenApi \
  --specification-url https://techcorp-employee-api.azurewebsites.net/v3/api-docs
```

### Paso 3: Configurar Seguridad

#### 3.1 Azure Key Vault para Secretos
```bash
# Crear Key Vault
az keyvault create \
  --name techcorp-keyvault \
  --resource-group rg-techcorp-api \
  --location eastus

# Almacenar secretos
az keyvault secret set \
  --vault-name techcorp-keyvault \
  --name "DB-Password" \
  --value "<password>"

# Dar acceso a App Service
az webapp identity assign \
  --resource-group rg-techcorp-api \
  --name techcorp-employee-api

az keyvault set-policy \
  --name techcorp-keyvault \
  --object-id <app-identity-id> \
  --secret-permissions get list
```

#### 3.2 Configurar Autenticación en APIM
```xml
<!-- Política en APIM para validar API Key -->
<policies>
    <inbound>
        <check-header name="Ocp-Apim-Subscription-Key" failed-check-httpcode="401" />
        <rate-limit calls="100" renewal-period="60" />
        <quota calls="10000" renewal-period="86400" />
    </inbound>
</policies>
```

### Paso 4: Monitoreo y Logs

```bash
# Habilitar Application Insights
az monitor app-insights component create \
  --app techcorp-insights \
  --location eastus \
  --resource-group rg-techcorp-api

# Conectar con App Service
az webapp config appsettings set \
  --resource-group rg-techcorp-api \
  --name techcorp-employee-api \
  --settings APPINSIGHTS_INSTRUMENTATIONKEY="<key>"
```

### Paso 5: Publicar la API

**URL Pública:**
```
https://techcorp-apim.azure-api.net/employees
```

**Documentación:**
```
https://techcorp-apim.developer.azure-api.net
```

**Developer Portal:**
- Portal auto-generado para desarrolladores
- Documentación interactiva (Swagger)
- Gestión de suscripciones
- Pruebas en vivo

---

## 5. Cómo Empresas Terceras Consumen Servicios {#consumo}

### Escenario: Empresa "ClienteCorp" consume API de TechCorp

### Paso 1: Registro en Developer Portal

```
1. Cliente accede a: https://techcorp-apim.developer.azure-api.net
2. Se registra con email
3. Recibe confirmación
4. Obtiene API Key (Subscription Key)
```

### Paso 2: Configuración del Cliente

#### Opción A: Consumo desde Aplicación Java/Spring Boot

```java
@Service
public class TechCorpApiClient {
    
    private final RestTemplate restTemplate;
    
    @Value("${techcorp.api.url}")
    private String apiUrl;
    
    @Value("${techcorp.api.key}")
    private String apiKey;
    
    public TechCorpApiClient() {
        this.restTemplate = new RestTemplate();
    }
    
    public List<Employee> getEmployees() {
        HttpHeaders headers = new HttpHeaders();
        headers.set("Ocp-Apim-Subscription-Key", apiKey);
        
        HttpEntity<String> entity = new HttpEntity<>(headers);
        
        ResponseEntity<List<Employee>> response = restTemplate.exchange(
            apiUrl + "/employees",
            HttpMethod.GET,
            entity,
            new ParameterizedTypeReference<List<Employee>>() {}
        );
        
        return response.getBody();
    }
    
    public Employee getEmployeeById(Long id) {
        HttpHeaders headers = new HttpHeaders();
        headers.set("Ocp-Apim-Subscription-Key", apiKey);
        
        HttpEntity<String> entity = new HttpEntity<>(headers);
        
        ResponseEntity<Employee> response = restTemplate.exchange(
            apiUrl + "/employees/" + id,
            HttpMethod.GET,
            entity,
            Employee.class
        );
        
        return response.getBody();
    }
}
```

**application.yml:**
```yaml
techcorp:
  api:
    url: https://techcorp-apim.azure-api.net
    key: ${TECHCORP_API_KEY}  # Variable de entorno
```

#### Opción B: Consumo desde Azure Logic App

```json
{
  "definition": {
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "actions": {
      "Get_Employees": {
        "type": "Http",
        "inputs": {
          "method": "GET",
          "uri": "https://techcorp-apim.azure-api.net/employees",
          "headers": {
            "Ocp-Apim-Subscription-Key": "@parameters('apiKey')"
          }
        }
      },
      "Process_Employees": {
        "type": "Foreach",
        "foreach": "@body('Get_Employees')",
        "actions": {
          "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
              "host": {
                "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
                }
              },
              "method": "post",
              "path": "/v2/Mail",
              "body": {
                "To": "hr@clientecorp.com",
                "Subject": "Nuevo empleado",
                "Body": "@{items('Process_Employees')}"
              }
            }
          }
        }
      }
    }
  }
}
```

#### Opción C: Consumo desde Azure Function

```csharp
public static class EmployeeSync
{
    [FunctionName("SyncEmployees")]
    public static async Task Run(
        [TimerTrigger("0 0 * * * *")] TimerInfo myTimer,
        ILogger log)
    {
        var apiKey = Environment.GetEnvironmentVariable("TECHCORP_API_KEY");
        var apiUrl = "https://techcorp-apim.azure-api.net/employees";
        
        using var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
        
        var response = await client.GetAsync(apiUrl);
        var employees = await response.Content.ReadAsAsync<List<Employee>>();
        
        // Procesar empleados
        log.LogInformation($"Sincronizados {employees.Count} empleados");
    }
}
```

### Paso 3: Gestión de Subscription Keys

**Tipos de Keys en APIM:**
- **Primary Key:** Uso principal
- **Secondary Key:** Rotación sin downtime

**Rotación de Keys:**
```bash
# Regenerar Secondary Key
az apim subscription renew-secret \
  --resource-group rg-techcorp-api \
  --service-name techcorp-apim \
  --sid <subscription-id> \
  --key-type secondary

# Cliente actualiza a Secondary Key en su config

# Regenerar Primary Key
az apim subscription renew-secret \
  --resource-group rg-techcorp-api \
  --service-name techcorp-apim \
  --sid <subscription-id> \
  --key-type primary
```

### Paso 4: Monitoreo del Consumo

**Desde Azure Portal (TechCorp):**
- Dashboard de APIM
- Métricas de uso por cliente
- Logs de errores
- Latencias

**Desde Application Insights (ClienteCorp):**
```java
@Component
public class ApiMonitoring {
    
    private TelemetryClient telemetry;
    
    @Autowired
    public ApiMonitoring(TelemetryClient telemetry) {
        this.telemetry = telemetry;
    }
    
    public void trackApiCall(String operation, long duration, boolean success) {
        Map<String, String> properties = new HashMap<>();
        properties.put("api", "techcorp-employee-api");
        properties.put("operation", operation);
        
        Map<String, Double> metrics = new HashMap<>();
        metrics.put("duration", (double) duration);
        
        telemetry.trackEvent(
            success ? "API_Call_Success" : "API_Call_Failure",
            properties,
            metrics
        );
    }
}
```

---

## 6. Casos de Uso Real {#casos-uso}

### Caso 1: E-commerce Publicando API de Productos

**Empresa:** "ShopNow" (Tienda online)

**Servicios Publicados:**
- API de Catálogo de Productos
- API de Inventario
- API de Precios

**Arquitectura:**
```
Azure Front Door
    ↓
Azure API Management
    ↓
┌───────────────┬────────────────┬──────────────┐
│ Product API   │ Inventory API  │ Pricing API  │
│ (App Service) │ (Functions)    │ (AKS)        │
└───────┬───────┴────────┬───────┴──────┬───────┘
        │                │              │
    Cosmos DB      SQL Database    Redis Cache
```

**Consumidores:**
- Marketplaces (Amazon, eBay)
- Apps móviles de terceros
- Sistemas de ERP externos

**Seguridad:**
- OAuth 2.0 para autenticación
- Rate limiting: 1000 requests/hora por cliente
- IP whitelisting para partners premium

---

### Caso 2: Banco Publicando API de Pagos

**Empresa:** "BancoSeguro"

**Servicios Publicados:**
- API de Transferencias
- API de Consulta de Saldo
- API de Transacciones

**Arquitectura:**
```
Azure Front Door (SSL Termination)
    ↓
Azure API Management
    ↓
Azure Application Gateway (WAF)
    ↓
AKS (Microservicios)
    ├── Transfer Service
    ├── Balance Service
    └── Transaction Service
    ↓
Azure SQL Database (Encrypted)
```

**Consumidores:**
- Fintechs
- Sistemas de punto de venta
- Apps de banca móvil

**Seguridad:**
- OAuth 2.0 + mTLS (mutual TLS)
- PSD2 Compliance
- Rate limiting estricto: 100 req/min
- Tokenización de datos sensibles
- Azure Key Vault para certificados

---

### Caso 3: Empresa de Logística con API de Tracking

**Empresa:** "FastDelivery"

**Servicios Publicados:**
- API de Tracking de Paquetes
- API de Cálculo de Rutas
- API de Notificaciones

**Arquitectura:**
```
Azure API Management
    ↓
┌────────────────┬──────────────────┬─────────────────┐
│ Tracking API   │ Route API        │ Notification API│
│ (App Service)  │ (Azure Maps)     │ (Event Grid)    │
└────────┬───────┴────────┬─────────┴────────┬────────┘
         │                │                   │
    Cosmos DB        Azure Maps        Service Bus
    (Geo-replicated)                   ↓
                                 Logic Apps
                                 ↓
                              Email/SMS
```

**Consumidores:**
- E-commerce sites
- Apps de clientes finales
- Sistemas de almacén

**Funcionalidades:**
- Webhooks para notificaciones en tiempo real
- GraphQL además de REST
- Subscripción por paquete (streaming)

---

## 7. Seguridad y Gobernanza {#seguridad}

### 7.1 Autenticación y Autorización

#### Opción 1: API Keys (Básico)
```
GET /api/employees
Headers:
  Ocp-Apim-Subscription-Key: abc123xyz789
```

**Pros:** Simple, rápido
**Contras:** Menos seguro, no identifica usuarios individuales

#### Opción 2: OAuth 2.0 (Recomendado)
```
1. Cliente solicita token a Azure AD
2. Azure AD valida credenciales
3. Azure AD devuelve access_token
4. Cliente usa token en API
5. APIM valida token con Azure AD
```

**Configuración en APIM:**
```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401">
            <openid-config url="https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration" />
            <audiences>
                <audience>api://techcorp-employee-api</audience>
            </audiences>
            <issuers>
                <issuer>https://sts.windows.net/{tenant}/</issuer>
            </issuers>
        </validate-jwt>
    </inbound>
</policies>
```

#### Opción 3: Certificados Cliente (mTLS)
Para máxima seguridad (banca, salud):
```xml
<policies>
    <inbound>
        <authentication-certificate thumbprint="ABC123" />
    </inbound>
</policies>
```

### 7.2 Rate Limiting y Quotas

```xml
<policies>
    <inbound>
        <!-- 100 llamadas por minuto -->
        <rate-limit calls="100" renewal-period="60" />
        
        <!-- 10,000 llamadas por día -->
        <quota calls="10000" renewal-period="86400" />
        
        <!-- Por operación específica -->
        <rate-limit-by-key calls="5" renewal-period="60" 
            counter-key="@(context.Request.IpAddress)" />
    </inbound>
</policies>
```

### 7.3 Transformación y Validación

```xml
<policies>
    <inbound>
        <!-- Validar request -->
        <validate-content unspecified-content-type-action="prevent" max-size="102400" />
        
        <!-- Transformar request -->
        <set-header name="X-Request-Source" exists-action="override">
            <value>Azure-APIM</value>
        </set-header>
        
        <!-- Ocultar header sensible -->
        <set-header name="Authorization" exists-action="delete" />
    </inbound>
    
    <outbound>
        <!-- Transformar response -->
        <set-header name="X-Powered-By" exists-action="delete" />
        
        <!-- Agregar metadata -->
        <set-body>
            @{
                var response = context.Response.Body.As<JObject>();
                response["metadata"] = new JObject(
                    new JProperty("timestamp", DateTime.UtcNow),
                    new JProperty("version", "1.0")
                );
                return response.ToString();
            }
        </set-body>
    </outbound>
</policies>
```

### 7.4 Costos y Facturación

**Modelo de Precios de APIM:**

| Tier | Precio/mes | Requests incluidas | Uso |
|------|------------|-------------------|-----|
| **Consumption** | $0 base + $3.50/millón | ∞ | Dev/Test, cargas variables |
| **Developer** | ~$50 | 1 millón | Desarrollo |
| **Basic** | ~$150 | 2 millones | Producción pequeña |
| **Standard** | ~$700 | 10 millones | Producción media |
| **Premium** | ~$2,800 | 20 millones | Enterprise, multi-región |

**Otros Costos:**
- App Service: $13-$730/mes según tier
- SQL Database: $5-$7,000/mes según DTU
- Storage: $0.02/GB/mes
- Bandwidth: $0.08/GB salida

**Ejemplo de Facturación Mensual:**
```
API Management (Standard):     $700
App Service (P1V2):            $150
SQL Database (S3):             $180
Application Insights:          $50
Storage (100GB):               $2
Bandwidth (500GB):             $40
──────────────────────────────────
TOTAL:                         $1,122/mes
```

### 7.5 Monitoreo de Costos

```bash
# Ver costos por recurso
az consumption usage list \
  --start-date 2024-12-01 \
  --end-date 2024-12-31 \
  --query "[?contains(instanceName, 'techcorp')]"

# Crear alerta de presupuesto
az consumption budget create \
  --budget-name techcorp-budget \
  --amount 2000 \
  --time-grain Monthly \
  --notifications \
    actual_GreaterThan_80_Percent \
    actual_GreaterThan_100_Percent
```

---

## 📊 Resumen Comparativo

### Azure vs Otras Nubes

| Característica | Azure | AWS | GCP |
|----------------|-------|-----|-----|
| **API Gateway** | API Management | API Gateway | Apigee |
| **Compute** | App Service | Elastic Beanstalk | App Engine |
| **Containers** | AKS | EKS | GKE |
| **Serverless** | Functions | Lambda | Cloud Functions |
| **Database** | SQL Database | RDS | Cloud SQL |
| **Identity** | Azure AD | IAM + Cognito | Cloud Identity |
| **Monitoreo** | Application Insights | CloudWatch | Cloud Monitoring |

### Cuándo Elegir Azure

✅ **Bueno para:**
- Empresas que usan Microsoft (Windows, Office 365, .NET)
- Aplicaciones híbridas (on-premise + cloud)
- Integración con Azure AD
- Cumplimiento normativo (muchas certificaciones)

⚠️ **Considerar alternativas si:**
- Necesitas más servicios especializados (AWS tiene más)
- Presupuesto muy ajustado (algunas alternativas son más baratas)
- Equipo sin experiencia en ecosistema Microsoft

---

## 🎯 Conclusiones

**Para Empresas que Publican:**
1. Usa Azure API Management como gateway
2. Implementa autenticación robusta (OAuth 2.0)
3. Configura rate limiting y quotas
4. Monitorea con Application Insights
5. Documenta en Developer Portal
6. Versiona tu API

**Para Empresas que Consumen:**
1. Registra tu app en Developer Portal
2. Guarda API Keys de forma segura (Key Vault)
3. Implementa retry logic y circuit breakers
4. Monitorea tus llamadas
5. Respeta rate limits
6. Maneja errores apropiadamente

**Mejores Prácticas:**
- Siempre usar HTTPS
- Implementar logging completo
- Hacer health checks regulares
- Tener plan de disaster recovery
- Actualizar dependencias regularmente
- Revisar costos mensualmente

---

## 🔗 Referencias

- [Azure Documentation](https://docs.microsoft.com/azure)
- [API Management Best Practices](https://docs.microsoft.com/azure/api-management/api-management-howto-policies)
- [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/)
- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

---

**Última Actualización:** Diciembre 2024