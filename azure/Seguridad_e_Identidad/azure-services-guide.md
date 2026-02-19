# 📘 Guía Completa de Servicios Azure

## Índice de Contenidos
1. [Azure API Management](#api-management)
2. [Azure Kubernetes Service (AKS)](#kubernetes)
3. [Azure Key Vault](#key-vault)
4. [Azure Redis Cache](#redis)
5. [Azure Cosmos DB](#cosmos)
6. [Azure App Service](#app-service)

---

## 1. AZURE API MANAGEMENT {#api-management}

### 📖 Definición Detallada

Azure API Management (APIM) es una plataforma completamente administrada que actúa como una **puerta de enlace (gateway)** entre los consumidores de APIs (clientes externos, aplicaciones móviles, partners) y los servicios backend. Proporciona un punto de entrada unificado para publicar, documentar, proteger, monitorear y monetizar APIs en cualquier plataforma.

**Componentes principales:**
- **Gateway API:** Punto de entrada que recibe llamadas API y las enruta a backends
- **Portal de Administración:** Interfaz para configurar políticas, backends y productos
- **Portal del Desarrollador:** Sitio web personalizable donde los desarrolladores pueden descubrir, probar y consumir APIs
- **Azure Portal:** Integración con el portal de Azure para gestión general

### 🎯 Importancia

**Por qué es crítico:**
1. **Seguridad centralizada:** Punto único para aplicar autenticación, autorización y rate limiting
2. **Desacoplamiento:** Los clientes no conocen las URLs reales de los backends
3. **Transformación de respuestas:** Modifica requests/responses sin cambiar código
4. **Analytics y monitoreo:** Métricas detalladas de uso de APIs
5. **Versionamiento:** Gestiona múltiples versiones de APIs sin interrumpir clientes
6. **Monetización:** Implementa planes de suscripción y facturación por uso

### ✨ Características Principales

**1. Gestión de APIs:**
- Importación desde OpenAPI/Swagger, WSDL, Azure Functions, Logic Apps
- Versionamiento y revisiones
- Productos y suscripciones
- Grupos de APIs relacionadas

**2. Políticas (Policies):**
- Rate limiting y cuotas
- Transformación de XML/JSON
- Caching de respuestas
- Validación de JWT
- IP filtering
- CORS
- Mock responses
- Reescritura de URLs

**3. Seguridad:**
- OAuth 2.0, OpenID Connect
- Certificados de cliente
- API Keys
- Integración con Azure AD
- Validación de tokens JWT
- Mutual TLS

**4. Backends Múltiples:**
- APIs REST/SOAP
- Azure Functions
- Logic Apps
- Service Fabric
- Kubernetes
- APIs on-premises (via VPN/ExpressRoute)

**5. Developer Portal:**
- Documentación interactiva
- Consola de pruebas
- Gestión de suscripciones
- Ejemplos de código en múltiples lenguajes

### ✅ Ventajas

1. **Centralización:** Un solo punto para gestionar todas las APIs
2. **Sin código en backends:** Las políticas se aplican en el gateway
3. **Multi-cloud y híbrido:** Puede exponer APIs de Azure, AWS, on-premises
4. **Escalabilidad automática:** Escala según demanda
5. **Reducción de latencia:** Caching integrado y distribución geográfica
6. **Cumplimiento normativo:** Facilita auditoría y logging centralizado
7. **Time to market:** Publica APIs rápidamente sin tocar código
8. **Experiencia de desarrollador:** Portal self-service para consumidores

### ❌ Desventajas

1. **Costo:** Tier Premium es costoso (desde ~$2,900/mes)
2. **Complejidad inicial:** Curva de aprendizaje en políticas XML
3. **Single point of failure:** Si APIM cae, todas las APIs son inaccesibles (mitigar con multi-region)
4. **Latencia adicional:** Agrega ~10-50ms por el hop extra
5. **Vendor lock-in:** Políticas específicas de Azure dificultan migración
6. **Limitaciones de transformación:** Transformaciones complejas requieren C# expressions
7. **Debugging:** Errores en políticas pueden ser difíciles de rastrear

### 🎯 Cuándo Aplicarlo

**✅ USAR cuando:**
- Tienes múltiples APIs que necesitan gestión centralizada
- Necesitas exponer APIs internas a partners/clientes externos
- Requieres rate limiting, quotas, y throttling
- Debes cumplir con estándares de seguridad (OAuth, JWT)
- Necesitas analytics detallados de uso de APIs
- Tienes APIs legacy (SOAP) que quieres exponer como REST
- Múltiples equipos desarrollan APIs y necesitas governance
- Quieres monetizar APIs con planes de suscripción
- Necesitas transformar requests/responses sin cambiar backend
- Arquitectura de microservicios con decenas de APIs

**❌ NO USAR cuando:**
- Tienes 1-2 APIs simples (overhead innecesario)
- Toda la comunicación es interna (usar Service Mesh como Istio)
- Latencia ultra-baja es crítica (<5ms)
- Presupuesto muy limitado (alternativas: Kong, Tyk)
- No necesitas funcionalidades avanzadas (usar Azure Front Door o Application Gateway)
- APIs puramente síncronas con alta frecuencia (millones RPM)

### 📋 Situaciones Específicas

**Escenario 1: Startup con 2-3 microservicios**
- ❌ No usar: Overhead de costo y complejidad
- ✅ Alternativa: Application Gateway con path-based routing

**Escenario 2: Empresa expone 50+ APIs a partners**
- ✅ Usar: Gestión centralizada, seguridad, analytics, developer portal

**Escenario 3: Migración de monolito a microservicios**
- ✅ Usar: Patrón Strangler Fig - APIM enruta gradualmente a nuevos servicios

**Escenario 4: IoT con millones de dispositivos**
- ❌ No usar directamente: Usar IoT Hub + APIM solo para management APIs

**Escenario 5: Banking APIs con regulación estricta**
- ✅ Usar: Auditoría, logging, seguridad, compliance

### 🔧 Políticas Comunes

#### 1. Rate Limiting por Suscripción
```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key calls="100" renewal-period="60" 
                          counter-key="@(context.Subscription.Id)" />
    </inbound>
</policies>
```

#### 2. Validación de JWT
```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401">
            <openid-config url="https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration" />
            <required-claims>
                <claim name="roles" match="any">
                    <value>Admin</value>
                    <value>User</value>
                </claim>
            </required-claims>
        </validate-jwt>
    </inbound>
</policies>
```

#### 3. Transformación JSON a XML
```xml
<policies>
    <inbound>
        <json-to-xml apply="always" consider-accept-header="false" />
    </inbound>
    <outbound>
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

#### 4. Caching de Respuestas
```xml
<policies>
    <inbound>
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-query-parameter>page</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### 5. CORS
```xml
<policies>
    <inbound>
        <cors allow-credentials="true">
            <allowed-origins>
                <origin>https://miapp.com</origin>
            </allowed-origins>
            <allowed-methods>
                <method>GET</method>
                <method>POST</method>
            </allowed-methods>
            <allowed-headers>
                <header>Content-Type</header>
                <header>Authorization</header>
            </allowed-headers>
        </cors>
    </inbound>
</policies>
```

#### 6. Retry con Exponential Backoff
```xml
<policies>
    <inbound>
        <retry condition="@(context.Response.StatusCode == 500)" count="3" 
               interval="1" max-interval="10" delta="2" first-fast-retry="true">
            <forward-request />
        </retry>
    </inbound>
</policies>
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear instancia de API Management

**Por Azure Portal:**
```bash
# Variables
RESOURCE_GROUP="rg-apim-demo"
LOCATION="eastus"
APIM_NAME="apim-empresa-dev"
PUBLISHER_EMAIL="admin@empresa.com"
PUBLISHER_NAME="Empresa SA"

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear APIM (Tier Developer para pruebas)
az apim create \
  --resource-group $RESOURCE_GROUP \
  --name $APIM_NAME \
  --location $LOCATION \
  --publisher-email $PUBLISHER_EMAIL \
  --publisher-name "$PUBLISHER_NAME" \
  --sku-name Developer \
  --enable-managed-identity true

# Tarda ~45 minutos en aprovisionarse
```

**Por Terraform:**
```hcl
resource "azurerm_api_management" "apim" {
  name                = "apim-empresa-dev"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  publisher_name      = "Empresa SA"
  publisher_email     = "admin@empresa.com"

  sku_name = "Developer_1"

  identity {
    type = "SystemAssigned"
  }

  protocols {
    enable_http2 = true
  }

  security {
    enable_backend_ssl30  = false
    enable_backend_tls10  = false
    enable_backend_tls11  = false
    enable_frontend_ssl30 = false
    enable_frontend_tls10 = false
    enable_frontend_tls11 = false
  }
}
```

#### Paso 2: Crear API desde OpenAPI

**Opción A: Importar desde Swagger JSON**
```bash
# Descargar tu OpenAPI spec
curl -o api-spec.json https://api.empresa.com/swagger/v1/swagger.json

# Importar a APIM
az apim api import \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --path "employees" \
  --display-name "Employee API" \
  --api-id "employee-api" \
  --specification-format OpenApi \
  --specification-path api-spec.json \
  --service-url "https://backend.empresa.com/api"
```

**Opción B: Crear manualmente**
```bash
# Crear API
az apim api create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --api-id employee-api \
  --path employees \
  --display-name "Employee API" \
  --service-url "https://backend.empresa.com/api" \
  --protocols https

# Agregar operación GET
az apim api operation create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --api-id employee-api \
  --operation-id get-employees \
  --display-name "List Employees" \
  --method GET \
  --url-template "/"
```

#### Paso 3: Aplicar Políticas

**Política a nivel de API (aplica a todas las operaciones):**
```bash
# Crear archivo policy.xml
cat > policy.xml << 'EOF'
<policies>
    <inbound>
        <base />
        <rate-limit-by-key calls="100" renewal-period="60" 
                          counter-key="@(context.Subscription.Id)" />
        <cors allow-credentials="true">
            <allowed-origins>
                <origin>*</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="300">
                <method>*</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
        </cors>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
        <cache-store duration="60" />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
EOF

# Aplicar política
az apim api policy create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --api-id employee-api \
  --policy-content @policy.xml
```

#### Paso 4: Crear Producto y Suscripción

```bash
# Crear producto
az apim product create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --product-id starter-plan \
  --product-name "Starter Plan" \
  --description "Plan básico con 1000 llamadas/mes" \
  --subscription-required true \
  --approval-required false \
  --state published

# Asociar API al producto
az apim product api add \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --product-id starter-plan \
  --api-id employee-api

# Crear suscripción para un usuario
az apim subscription create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --subscription-id demo-subscription \
  --scope /products/starter-plan \
  --display-name "Demo Subscription" \
  --state active

# Obtener las keys
az apim subscription show \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --subscription-id demo-subscription \
  --query '{primary: primaryKey, secondary: secondaryKey}'
```

#### Paso 5: Configurar Custom Domain (Opcional)

```bash
# Subir certificado SSL
az apim certificate create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --certificate-id api-cert \
  --certificate-file certificate.pfx \
  --certificate-password "P@ssw0rd"

# Configurar dominio personalizado
az apim update \
  --resource-group $RESOURCE_GROUP \
  --name $APIM_NAME \
  --set hostnameConfigurations[0].hostName='api.empresa.com' \
  --set hostnameConfigurations[0].type='Proxy' \
  --set hostnameConfigurations[0].certificateId='api-cert'
```

### 💻 Ejemplo con Spring Boot

#### Configuración del Backend (Spring Boot)

**1. Dependencias (pom.xml)**
```xml
<dependencies>
    <!-- Spring Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Swagger/OpenAPI para documentación -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        <version>2.3.0</version>
    </dependency>
    
    <!-- Para validar tokens JWT de APIM -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
    </dependency>
</dependencies>
```

**2. Configuración (application.yml)**
```yaml
spring:
  application:
    name: employee-api
  security:
    oauth2:
      resourceserver:
        jwt:
          # Si APIM valida JWT, el backend puede confiar en el header
          issuer-uri: https://login.microsoftonline.com/{tenant}/v2.0

# OpenAPI configuration
springdoc:
  api-docs:
    path: /api-docs
  swagger-ui:
    path: /swagger-ui.html

# Server configuration
server:
  port: 8080
  servlet:
    context-path: /api

# APIM configurará este endpoint como backend
# URL completa: https://backend.empresa.com/api
```

**3. Controller con Anotaciones OpenAPI**
```java
@RestController
@RequestMapping("/employees")
@Tag(name = "Employee API", description = "Gestión de empleados")
public class EmployeeController {
    
    @Autowired
    private EmployeeService service;
    
    @GetMapping
    @Operation(
        summary = "Listar empleados",
        description = "Retorna todos los empleados con paginación"
    )
    @ApiResponses({
        @ApiResponse(responseCode = "200", description = "Lista obtenida exitosamente"),
        @ApiResponse(responseCode = "401", description = "No autorizado"),
        @ApiResponse(responseCode = "429", description = "Límite de rate excedido")
    })
    public ResponseEntity<List<Employee>> listEmployees(
        @Parameter(description = "Número de página") @RequestParam(defaultValue = "0") int page,
        @Parameter(description = "Tamaño de página") @RequestParam(defaultValue = "10") int size
    ) {
        List<Employee> employees = service.findAll(page, size);
        return ResponseEntity.ok(employees);
    }
    
    @GetMapping("/{id}")
    @Operation(summary = "Obtener empleado por ID")
    public ResponseEntity<Employee> getEmployee(
        @Parameter(description = "ID del empleado") @PathVariable Long id
    ) {
        return service.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    @Operation(summary = "Crear nuevo empleado")
    public ResponseEntity<Employee> createEmployee(
        @RequestBody @Valid Employee employee
    ) {
        Employee created = service.save(employee);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }
}
```

**4. Configuración de Seguridad (si APIM valida JWT)**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                // Permitir endpoints de health y docs
                .requestMatchers("/actuator/health", "/api-docs/**", "/swagger-ui/**").permitAll()
                // Todo lo demás requiere autenticación
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt
                    .jwtAuthenticationConverter(jwtAuthenticationConverter())
                )
            );
        
        return http.build();
    }
    
    @Bean
    public JwtAuthenticationConverter jwtAuthenticationConverter() {
        JwtAuthenticationConverter converter = new JwtAuthenticationConverter();
        converter.setJwtGrantedAuthoritiesConverter(jwt -> {
            // Extraer roles del claim "roles" del JWT
            List<String> roles = jwt.getClaimAsStringList("roles");
            if (roles == null) {
                return Collections.emptyList();
            }
            return roles.stream()
                .map(role -> new SimpleGrantedAuthority("ROLE_" + role))
                .collect(Collectors.toList());
        });
        return converter;
    }
}
```

**5. Exportar OpenAPI Spec para APIM**
```java
@RestController
@RequestMapping("/api-docs")
public class OpenApiController {
    
    @GetMapping(value = "/export", produces = "application/json")
    public ResponseEntity<String> exportOpenApi() {
        // Generar JSON de OpenAPI
        String openApiJson = new OpenApiResource().getOpenApiJson();
        return ResponseEntity.ok(openApiJson);
    }
}
```

**6. Desplegar a Azure App Service**
```bash
# Crear App Service
az webapp create \
  --resource-group $RESOURCE_GROUP \
  --plan myAppServicePlan \
  --name employee-backend \
  --runtime "JAVA:17-java17"

# Desplegar JAR
mvn clean package
az webapp deploy \
  --resource-group $RESOURCE_GROUP \
  --name employee-backend \
  --src-path target/employee-api-1.0.0.jar

# URL del backend: https://employee-backend.azurewebsites.net
```

**7. Importar a APIM**
```bash
# Descargar OpenAPI spec del backend
curl https://employee-backend.azurewebsites.net/api/api-docs > openapi.json

# Importar a APIM
az apim api import \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --path "v1/employees" \
  --api-id employee-api-v1 \
  --specification-format OpenApiJson \
  --specification-path openapi.json \
  --service-url "https://employee-backend.azurewebsites.net/api"
```

### 💻 Ejemplo con Quarkus

#### Configuración del Backend (Quarkus)

**1. Dependencias (pom.xml)**
```xml
<dependencies>
    <!-- Quarkus REST -->
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-resteasy-reactive-jackson</artifactId>
    </dependency>
    
    <!-- OpenAPI/Swagger -->
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-smallrye-openapi</artifactId>
    </dependency>
    
    <!-- JWT Security -->
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-smallrye-jwt</artifactId>
    </dependency>
    
    <!-- Health checks -->
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-smallrye-health</artifactId>
    </dependency>
</dependencies>
```

**2. Configuración (application.properties)**
```properties
# Application
quarkus.application.name=employee-api
quarkus.http.port=8080
quarkus.http.root-path=/api

# OpenAPI
quarkus.smallrye-openapi.path=/api-docs
quarkus.swagger-ui.always-include=true
quarkus.swagger-ui.path=/swagger-ui

# JWT Security (si APIM envía JWT)
mp.jwt.verify.publickey.location=https://login.microsoftonline.com/${tenant}/discovery/v2.0/keys
mp.jwt.verify.issuer=https://login.microsoftonline.com/${tenant}/v2.0

# CORS (será manejado por APIM, pero podemos configurar por si acaso)
quarkus.http.cors=true
quarkus.http.cors.origins=https://api.empresa.com
quarkus.http.cors.methods=GET,POST,PUT,DELETE
quarkus.http.cors.headers=accept,authorization,content-type

# Health checks para APIM backend health probe
quarkus.smallrye-health.root-path=/health
```

**3. Resource con Anotaciones OpenAPI**
```java
@Path("/employees")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
@Tag(name = "Employee API", description = "Gestión de empleados")
public class EmployeeResource {
    
    @Inject
    EmployeeService service;
    
    @GET
    @Operation(
        summary = "Listar empleados",
        description = "Retorna todos los empleados con paginación"
    )
    @APIResponses({
        @APIResponse(responseCode = "200", description = "Lista obtenida"),
        @APIResponse(responseCode = "401", description = "No autorizado"),
        @APIResponse(responseCode = "429", description = "Rate limit excedido")
    })
    @RolesAllowed({"User", "Admin"}) // Si usas JWT
    public List<Employee> list(
        @QueryParam("page") @DefaultValue("0") int page,
        @QueryParam("size") @DefaultValue("10") int size
    ) {
        return service.findAll(page, size);
    }
    
    @GET
    @Path("/{id}")
    @Operation(summary = "Obtener empleado por ID")
    public Response getById(@PathParam("id") Long id) {
        return service.findById(id)
            .map(emp -> Response.ok(emp).build())
            .orElse(Response.status(Response.Status.NOT_FOUND).build());
    }
    
    @POST
    @Operation(summary = "Crear empleado")
    @RolesAllowed("Admin")
    public Response create(@Valid Employee employee) {
        Employee created = service.save(employee);
        return Response.status(Response.Status.CREATED).entity(created).build();
    }
}
```

**4. Custom Health Check**
```java
@ApplicationScoped
public class DatabaseHealthCheck implements HealthCheck {
    
    @Inject
    DataSource dataSource;
    
    @Override
    public HealthCheckResponse call() {
        HealthCheckResponseBuilder builder = HealthCheckResponse.named("Database");
        
        try (Connection conn = dataSource.getConnection()) {
            boolean isHealthy = conn.isValid(3);
            if (isHealthy) {
                builder.up();
            } else {
                builder.down();
            }
        } catch (Exception e) {
            builder.down().withData("error", e.getMessage());
        }
        
        return builder.build();
    }
}
```

**5. Construir imagen nativa para mejor rendimiento**
```bash
# Compilar a nativo (requiere GraalVM)
./mvnw package -Pnative -Dquarkus.native.container-build=true

# Construir imagen Docker
docker build -f src/main/docker/Dockerfile.native -t employee-api:1.0.0 .

# Subir a Azure Container Registry
az acr login --name myregistry
docker tag employee-api:1.0.0 myregistry.azurecr.io/employee-api:1.0.0
docker push myregistry.azurecr.io/employee-api:1.0.0
```

**6. Desplegar a Azure Container Apps**
```bash
# Crear Container App
az containerapp create \
  --name employee-api \
  --resource-group $RESOURCE_GROUP \
  --environment myenv \
  --image myregistry.azurecr.io/employee-api:1.0.0 \
  --target-port 8080 \
  --ingress external \
  --min-replicas 1 \
  --max-replicas 10 \
  --cpu 0.5 \
  --memory 1.0Gi

# Obtener URL
az containerapp show \
  --name employee-api \
  --resource-group $RESOURCE_GROUP \
  --query properties.configuration.ingress.fqdn
```

**7. Importar a APIM desde Quarkus**
```bash
# URL del OpenAPI: https://employee-api.{region}.azurecontainerapps.io/api/api-docs

az apim api import \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --path "v1/employees" \
  --api-id employee-api-quarkus \
  --specification-format OpenApi \
  --specification-url "https://employee-api.{region}.azurecontainerapps.io/api/api-docs" \
  --service-url "https://employee-api.{region}.azurecontainerapps.io/api"
```

### 📊 Comparación de Tiers

| Feature | Consumption | Developer | Basic | Standard | Premium |
|---------|-------------|-----------|-------|----------|---------|
| **Precio/mes** | Pay-per-use | ~$50 | ~$150 | ~$700 | ~$2,900 |
| **Gateway units** | Serverless | 1 | 2 | 4 | Variable |
| **Requests/seg** | Variable | 500 | 1,000 | 2,500 | 4,000+ por unit |
| **SLA** | Ninguno | Ninguno | 99.95% | 99.95% | 99.99% |
| **Multi-region** | ❌ | ❌ | ❌ | ❌ | ✅ |
| **VNet integration** | ❌ | ❌ | ❌ | ❌ | ✅ |
| **Custom domains** | ❌ | ✅ | ✅ | ✅ | ✅ |
| **Self-hosted gateway** | ❌ | ❌ | ❌ | ❌ | ✅ |
| **Cache** | ❌ | ✅ (externo) | ✅ (externo) | ✅ (externo) | ✅ (interno) |
| **Uso recomendado** | Dev/test | Dev/test | Staging | Producción | Enterprise |

### 🔍 Monitoreo y Observabilidad

**Application Insights Integration:**
```bash
# Crear Application Insights
az monitor app-insights component create \
  --app apim-insights \
  --location $LOCATION \
  --resource-group $RESOURCE_GROUP

# Obtener instrumentation key
INSTRUMENTATION_KEY=$(az monitor app-insights component show \
  --app apim-insights \
  --resource-group $RESOURCE_GROUP \
  --query instrumentationKey -o tsv)

# Configurar APIM para enviar telemetría
az apim logger create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --logger-id app-insights-logger \
  --logger-type applicationInsights \
  --credentials instrumentationKey=$INSTRUMENTATION_KEY

# Activar logging en la API
az apim api diagnostic create \
  --resource-group $RESOURCE_GROUP \
  --service-name $APIM_NAME \
  --api-id employee-api \
  --diagnostic-id applicationinsights \
  --logger-id app-insights-logger \
  --sampling-percentage 100 \
  --always-log allErrors
```

**Consultas útiles en Log Analytics:**
```kusto
// Top 10 APIs más llamadas
ApiManagementGatewayLogs
| where TimeGenerated > ago(24h)
| summarize count() by ApiId
| top 10 by count_

// Tasa de error por API
ApiManagementGatewayLogs
| where TimeGenerated > ago(1h)
| summarize 
    Total = count(),
    Errors = countif(ResponseCode >= 400)
    by ApiId
| extend ErrorRate = (Errors * 100.0) / Total
| project ApiId, ErrorRate
| order by ErrorRate desc

// Latencia promedio (P50, P95, P99)
ApiManagementGatewayLogs
| where TimeGenerated > ago(1h)
| summarize 
    P50 = percentile(BackendTime, 50),
    P95 = percentile(BackendTime, 95),
    P99 = percentile(BackendTime, 99)
    by ApiId
```

---

*Continuará con Azure Kubernetes Service...*