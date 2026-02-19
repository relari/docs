# 🔐 Azure Key Vault - Guía Completa

## 📖 Definición Detallada

Azure Key Vault es un **servicio de gestión de secretos completamente administrado** que proporciona almacenamiento seguro y acceso controlado a secretos, claves criptográficas y certificados. Actúa como una bóveda centralizada para proteger información sensible y eliminar la necesidad de almacenar credenciales en código, archivos de configuración o variables de entorno.

**Componentes principales:**

**1. Secrets (Secretos):**
- Cadenas de texto sensibles (contraseñas, connection strings, API keys)
- Versionamiento automático (historial completo de cambios)
- Políticas de expiración y activación programada
- Tamaño máximo: 25 KB por secret

**2. Keys (Claves Criptográficas):**
- Claves RSA, EC (Elliptic Curve) para cifrado/firma
- **Software-protected keys:** Almacenadas en software (más económico)
- **HSM-protected keys:** Almacenadas en Hardware Security Modules FIPS 140-2 Level 2/3
- Operaciones: Encrypt, Decrypt, Sign, Verify, Wrap, Unwrap
- Soporte para Azure Disk Encryption, Transparent Data Encryption (TDE)

**3. Certificates (Certificados):**
- Certificados X.509 para SSL/TLS
- Integración con Certificate Authorities (DigiCert, GlobalSign)
- Auto-renovación antes de expiración
- Conversión automática a secrets (private key + certificate)
- Formato: PFX/PEM

**Arquitectura de seguridad:**
- **Encryption at Rest:** Todos los datos cifrados con claves gestionadas por Microsoft
- **Encryption in Transit:** TLS 1.2+ obligatorio
- **Access Policies / RBAC:** Control granular de permisos
- **Soft-delete:** Recuperación de objetos eliminados (7-90 días)
- **Purge protection:** Previene eliminación permanente accidental
- **Private Endpoints:** Acceso privado vía VNet sin internet
- **Firewall:** Restricción por IP y VNet
- **Audit logs:** Registro completo de accesos en Azure Monitor

### 🎯 Importancia

**Por qué Key Vault es crítico en seguridad:**

1. **Eliminación de hard-coded secrets:** Código sin credenciales → reducción de riesgo de fuga
2. **Cumplimiento normativo:** GDPR, HIPAA, PCI-DSS, SOC 2 exigen gestión centralizada de secretos
3. **Separación de concerns:** Desarrolladores no tienen acceso a secretos de producción
4. **Rotación automatizada:** Cambio periódico de contraseñas sin downtime
5. **Auditoría completa:** Quién accedió a qué secreto y cuándo
6. **Recuperación ante desastres:** Backup automático con geo-replicación
7. **Integración nativa:** Azure services (App Service, Functions, AKS) sin código adicional
8. **Criptografía como servicio:** Cifrado/descifrado sin gestionar claves localmente

### ✨ Características Principales

**1. Gestión de Secretos:**
- Versionamiento automático (hasta 25 versiones por defecto)
- Content-Type para identificar formato (JSON, connection string, etc.)
- Tags para organización y búsqueda
- Fechas de activación y expiración
- Habilitado/deshabilitado sin eliminar

**2. Claves Criptográficas:**
- **Algoritmos soportados:**
  - RSA: 2048, 3072, 4096 bits
  - EC: P-256, P-256K, P-384, P-521
- **Operaciones criptográficas:**
  - Encrypt/Decrypt (RSA-OAEP, RSA1_5)
  - Sign/Verify (RSA-PSS, ECDSA)
  - Wrap/Unwrap (para proteger otras claves)
- **Bring Your Own Key (BYOK):** Importar claves desde HSM on-premises
- **Key rotation:** Manual o automática con políticas

**3. Certificados SSL/TLS:**
- Emisión automática vía integrated CAs (DigiCert, GlobalSign)
- Importación de certificados externos
- Auto-renovación 30 días antes de expiración
- Alertas de expiración vía Azure Monitor
- Exportación como PFX con private key protegida

**4. Control de Acceso:**
- **Access Policies (Legacy):** Permisos específicos por objeto (Get Secret, List Keys, etc.)
- **RBAC (Recomendado):** Roles de Azure (Key Vault Administrator, Secrets User, etc.)
- **Managed Identities:** App Service, Functions, VMs, AKS acceden sin credenciales
- **Service Principals:** Para aplicaciones externas
- **User identities:** Azure AD users y grupos

**5. Alta Disponibilidad:**
- **Geo-replicación automática:** Datos replicados a región secundaria
- **SLA 99.99%:** Con geo-redundancy
- **Soft-delete enabled by default:** Recuperación en 7-90 días
- **Purge protection:** Previene eliminación permanente durante período de retención

**6. Seguridad de Red:**
- **Private Endpoints:** Key Vault sin IP pública, acceso vía Private Link
- **Firewall rules:** Whitelist de IPs públicas
- **Service Endpoints:** Acceso desde VNets específicas
- **Bypass para Azure Services:** App Service, Functions pueden acceder desde cualquier IP

**7. Monitoreo y Auditoría:**
- **Diagnostic logs:** Todos los accesos registrados en Log Analytics
- **Métricas:** Request count, latency, availability
- **Azure Monitor Alerts:** Notificaciones por accesos sospechosos, fallos, expiración
- **Azure Sentinel:** Detección de amenazas con ML

**8. Integración con Azure Services:**
- **App Service / Functions:** Referencias directas a secretos en configuración
- **AKS:** Secrets Store CSI Driver para montar secrets en pods
- **Azure DevOps / GitHub Actions:** Secrets en pipelines CI/CD
- **Azure SQL / Cosmos DB:** Connection strings sin hard-coding
- **Azure Storage:** Claves de acceso rotables
- **Application Insights:** Instrumentación keys

### ✅ Ventajas

1. **Zero-trust architecture:** Aplicaciones no almacenan credenciales
2. **Desarrollo seguro:** Desarrolladores trabajan sin acceso a secretos de prod
3. **Cumplimiento automático:** Logs y auditoría para regulaciones
4. **Sin gestión de infraestructura:** Servicio completamente administrado
5. **Escalabilidad:** Soporta miles de secretos por vault
6. **Costo-efectivo:** Tier Standard desde $0.03/10k operaciones
7. **Integración sin código:** Managed Identities eliminan credenciales en código
8. **Recuperación rápida:** Soft-delete permite restaurar en minutos
9. **HSM sin hardware:** Tier Premium con HSM FIPS 140-2 Level 2
10. **Multi-tenancy:** Un vault por ambiente (dev, staging, prod)

### ❌ Desventajas

1. **Latencia de red:** Cada acceso es una llamada HTTP (~20-100ms)
2. **Costo acumulado:** Operaciones frecuentes (millones/día) pueden ser caras
3. **Límites de rate:** 2,000 operaciones/10 segundos por vault (throttling)
4. **Cache necesaria:** Apps deben cachear secretos para reducir llamadas
5. **Complejidad inicial:** Configuración de RBAC y Managed Identities
6. **Lock-in de Azure:** Migrar a AWS Secrets Manager o HashiCorp Vault requiere refactoring
7. **Propagación lenta:** Cambios tardan segundos en replicarse
8. **Debugging complejo:** Errores de permisos difíciles de diagnosticar
9. **Sin versionamiento granular de keys:** Solo de secrets
10. **Tamaño limitado:** Secrets de 25KB máximo (problemático para archivos grandes)

### 🎯 Cuándo Aplicarlo

**✅ USAR Azure Key Vault cuando:**

1. **Aplicaciones en Azure:** App Service, Functions, AKS, VMs
2. **Cumplimiento normativo:** GDPR, HIPAA, PCI-DSS, SOC 2
3. **Múltiples ambientes:** Dev, staging, prod con diferentes secretos
4. **Rotación de secretos:** Contraseñas deben cambiar periódicamente
5. **Equipos distribuidos:** Developers, DevOps, SRE con diferentes permisos
6. **Auditoría requerida:** Necesitas rastrear quién accedió a qué
7. **Certificados SSL:** Gestión centralizada de certs para múltiples apps
8. **Cifrado de datos:** Necesitas claves HSM para TDE, Azure Disk Encryption
9. **Integración con CI/CD:** Secretos en Azure DevOps o GitHub Actions
10. **Multi-cloud híbrido:** Aplicaciones on-premises que acceden a Azure

**Ejemplos específicos:**
- E-commerce con PCI-DSS: Claves de encriptación de tarjetas
- Healthcare con HIPAA: Connection strings de bases de datos con PHI
- SaaS multi-tenant: Secretos por cliente con RBAC segregado
- Microservicios en AKS: Cada servicio accede a sus secretos vía Managed Identity
- DevOps pipelines: API keys de servicios externos (Stripe, Twilio)

**❌ NO USAR Azure Key Vault cuando:**

1. **Aplicación fuera de Azure sin VPN:** Latencia y costos de egress
2. **Configuración no sensible:** Feature flags, URLs públicas → usar App Configuration
3. **Archivos grandes:** Certificados > 25KB, binaries → usar Azure Storage
4. **Secretos efímeros:** Tokens de corta vida (< 5 min) → generar on-demand
5. **Open-source self-hosted:** Usa HashiCorp Vault (más portable)
6. **Presupuesto muy limitado:** Millones de operaciones/día pueden costar cientos de dólares
7. **Latencia crítica:** < 10ms requerido → cache local o in-memory secrets
8. **Regulaciones que prohíben cloud:** Algunos gobiernos/industrias
9. **Aplicación monolítica simple:** 1-2 secretos → variables de entorno cifradas
10. **Sin Managed Identity:** Si debes usar Service Principals, evalúa alternativas

### 📋 Situaciones Específicas

**Escenario 1: Startup con app en Heroku**
- ❌ Key Vault: Latencia transatlántica y sin Managed Identity
- ✅ Alternativa: Heroku Config Vars o AWS Secrets Manager

**Escenario 2: Empresa con 50+ microservicios en AKS**
- ✅ Key Vault: Con Secrets Store CSI Driver y Workload Identity

**Escenario 3: Aplicación legacy .NET Framework on-premises**
- ⚠️ Key Vault: Posible si hay Site-to-Site VPN, pero considerar complejidad
- ✅ Mejor: HashiCorp Vault on-premises

**Escenario 4: Aplicación Node.js en Azure Functions**
- ✅ Key Vault: Referencias directas sin código adicional

**Escenario 5: Base de datos con 1000s lecturas/segundo**
- ❌ No consultar Key Vault en cada query (rate limits)
- ✅ Cachear connection string en memoria con refresh cada 5 minutos

**Escenario 6: Certificados SSL para 100 dominios**
- ✅ Key Vault: Gestión centralizada con auto-renovación

**Escenario 7: Aplicación que cifra millones de registros/día**
- ❌ No llamar Key Vault para cada cifrado (throttling)
- ✅ Usar Key Vault para proteger data encryption key (DEK), cifrar con DEK en memoria

**Escenario 8: CI/CD pipeline en GitHub Actions**
- ✅ Key Vault: Con GitHub OIDC authentication (sin secretos en repo)

### 🔧 Políticas y Mejores Prácticas

#### 1. Access Policies vs RBAC

**Access Policies (Legacy):**
```bash
# Dar permisos específicos a un Service Principal
az keyvault set-policy \
  --name mykeyvault \
  --spn <service-principal-id> \
  --secret-permissions get list \
  --key-permissions get list \
  --certificate-permissions get list
```

**RBAC (Recomendado - más granular):**
```bash
# Habilitar RBAC en el vault
az keyvault update \
  --name mykeyvault \
  --resource-group myResourceGroup \
  --enable-rbac-authorization true

# Asignar rol de "Key Vault Secrets User" a una Managed Identity
az role assignment create \
  --role "Key Vault Secrets User" \
  --assignee <managed-identity-principal-id> \
  --scope /subscriptions/<sub-id>/resourceGroups/<rg>/providers/Microsoft.KeyVault/vaults/mykeyvault
```

**Roles comunes:**
- **Key Vault Administrator:** Control total (producción solo para SRE)
- **Key Vault Secrets User:** Leer secretos (aplicaciones)
- **Key Vault Secrets Officer:** Gestionar secretos (DevOps)
- **Key Vault Crypto User:** Usar claves para cifrado
- **Key Vault Certificate User:** Leer certificados

#### 2. Separación por Ambientes

```bash
# Crear vaults separados por ambiente
az keyvault create --name kv-myapp-dev --resource-group rg-dev
az keyvault create --name kv-myapp-staging --resource-group rg-staging
az keyvault create --name kv-myapp-prod --resource-group rg-prod

# Aplicar tags para organización
az keyvault update --name kv-myapp-prod \
  --tags Environment=Production CostCenter=Engineering Compliance=PCI-DSS
```

#### 3. Soft-Delete y Purge Protection

```bash
# Habilitar soft-delete (retention 90 días) y purge protection
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \
  --enable-soft-delete true \
  --retention-days 90 \
  --enable-purge-protection true

# Recuperar vault eliminado
az keyvault recover --name mykeyvault

# Listar vaults eliminados
az keyvault list-deleted
```

#### 4. Firewall y Private Endpoint

**Firewall rules:**
```bash
# Denegar todo por defecto
az keyvault update \
  --name mykeyvault \
  --resource-group myResourceGroup \
  --default-action Deny

# Permitir IPs específicas
az keyvault network-rule add \
  --name mykeyvault \
  --resource-group myResourceGroup \
  --ip-address 203.0.113.0/24

# Permitir VNet específica
az keyvault network-rule add \
  --name mykeyvault \
  --resource-group myResourceGroup \
  --vnet-name myVNet \
  --subnet mySubnet

# Bypass para servicios de confianza (App Service, Functions)
az keyvault update \
  --name mykeyvault \
  --resource-group myResourceGroup \
  --bypass AzureServices
```

**Private Endpoint (acceso privado sin internet):**
```bash
# Crear Private Endpoint
az network private-endpoint create \
  --name pe-keyvault \
  --resource-group myResourceGroup \
  --vnet-name myVNet \
  --subnet mySubnet \
  --private-connection-resource-id $(az keyvault show --name mykeyvault --query id -o tsv) \
  --group-id vault \
  --connection-name keyvault-connection

# Configurar Private DNS Zone
az network private-dns zone create \
  --resource-group myResourceGroup \
  --name privatelink.vaultcore.azure.net

az network private-dns link vnet create \
  --resource-group myResourceGroup \
  --zone-name privatelink.vaultcore.azure.net \
  --name dns-link \
  --virtual-network myVNet \
  --registration-enabled false

# Crear DNS record
az network private-endpoint dns-zone-group create \
  --resource-group myResourceGroup \
  --endpoint-name pe-keyvault \
  --name zone-group \
  --private-dns-zone privatelink.vaultcore.azure.net \
  --zone-name vault
```

#### 5. Monitoring y Alertas

```bash
# Crear Log Analytics Workspace
az monitor log-analytics workspace create \
  --resource-group myResourceGroup \
  --workspace-name logs-keyvault

# Habilitar diagnostic logs
WORKSPACE_ID=$(az monitor log-analytics workspace show \
  --resource-group myResourceGroup \
  --workspace-name logs-keyvault \
  --query id -o tsv)

az monitor diagnostic-settings create \
  --name keyvault-diagnostics \
  --resource $(az keyvault show --name mykeyvault --query id -o tsv) \
  --logs '[{"category": "AuditEvent", "enabled": true}]' \
  --metrics '[{"category": "AllMetrics", "enabled": true}]' \
  --workspace $WORKSPACE_ID

# Crear alerta por accesos fallidos
az monitor metrics alert create \
  --name alert-keyvault-failures \
  --resource-group myResourceGroup \
  --scopes $(az keyvault show --name mykeyvault --query id -o tsv) \
  --condition "avg ServiceApiResult == Forbidden" \
  --window-size 5m \
  --evaluation-frequency 1m \
  --action <action-group-id>
```

#### 6. Secret Rotation Policy

```bash
# Crear secret con expiración
az keyvault secret set \
  --vault-name mykeyvault \
  --name database-password \
  --value "OldPassword123!" \
  --expires "2025-12-31T23:59:59Z"

# Actualizar (crea nueva versión automáticamente)
az keyvault secret set \
  --vault-name mykeyvault \
  --name database-password \
  --value "NewPassword456!"

# Listar versiones
az keyvault secret list-versions \
  --vault-name mykeyvault \
  --name database-password
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear Key Vault

**Opción A: Azure CLI**
```bash
# Variables
RESOURCE_GROUP="rg-keyvault-prod"
LOCATION="eastus"
VAULT_NAME="kv-myapp-prod-001"  # Nombre globalmente único

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear Key Vault (Standard tier)
az keyvault create \
  --name $VAULT_NAME \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --sku standard \
  --enable-rbac-authorization true \
  --enable-soft-delete true \
  --retention-days 90 \
  --enable-purge-protection true \
  --default-action Deny \
  --bypass AzureServices

# Para tier Premium (con HSM)
az keyvault create \
  --name kv-myapp-prod-hsm \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --sku premium \
  --enable-rbac-authorization true
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
data "azurerm_client_config" "current" {}

resource "azurerm_resource_group" "rg" {
  name     = "rg-keyvault-${var.environment}"
  location = var.location
}

resource "azurerm_key_vault" "kv" {
  name                       = "kv-myapp-${var.environment}-001"
  location                   = azurerm_resource_group.rg.location
  resource_group_name        = azurerm_resource_group.rg.name
  tenant_id                  = data.azurerm_client_config.current.tenant_id
  sku_name                   = "standard"
  
  # Seguridad
  enable_rbac_authorization  = true
  soft_delete_retention_days = 90
  purge_protection_enabled   = true
  
  # Network
  network_acls {
    default_action             = "Deny"
    bypass                     = "AzureServices"
    ip_rules                   = ["203.0.113.0/24"]
    virtual_network_subnet_ids = [azurerm_subnet.app_subnet.id]
  }
  
  # Monitoring
  enabled_for_disk_encryption     = true
  enabled_for_deployment          = true
  enabled_for_template_deployment = true
  
  tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}

# Private Endpoint
resource "azurerm_private_endpoint" "kv_endpoint" {
  name                = "pe-${azurerm_key_vault.kv.name}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.pe_subnet.id

  private_service_connection {
    name                           = "psc-keyvault"
    private_connection_resource_id = azurerm_key_vault.kv.id
    is_manual_connection           = false
    subresource_names              = ["vault"]
  }
}

# Private DNS Zone
resource "azurerm_private_dns_zone" "kv_dns" {
  name                = "privatelink.vaultcore.azure.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "kv_dns_link" {
  name                  = "vnet-link"
  resource_group_name   = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.kv_dns.name
  virtual_network_id    = azurerm_virtual_network.vnet.id
}

# Log Analytics para monitoring
resource "azurerm_log_analytics_workspace" "logs" {
  name                = "logs-keyvault-${var.environment}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}

resource "azurerm_monitor_diagnostic_setting" "kv_diagnostics" {
  name                       = "kv-diagnostics"
  target_resource_id         = azurerm_key_vault.kv.id
  log_analytics_workspace_id = azurerm_log_analytics_workspace.logs.id

  enabled_log {
    category = "AuditEvent"
  }

  metric {
    category = "AllMetrics"
  }
}

# outputs.tf
output "key_vault_name" {
  value = azurerm_key_vault.kv.name
}

output "key_vault_uri" {
  value = azurerm_key_vault.kv.vault_uri
}
```

#### Paso 2: Agregar Secretos, Keys y Certificados

**Secretos:**
```bash
# Agregar secret simple
az keyvault secret set \
  --vault-name $VAULT_NAME \
  --name "database-password" \
  --value "MySecurePassword123!"

# Agregar secret con metadata
az keyvault secret set \
  --vault-name $VAULT_NAME \
  --name "stripe-api-key" \
  --value "sk_live_123456789" \
  --description "Stripe API Key for production" \
  --content-type "text/plain" \
  --expires "2025-12-31T23:59:59Z" \
  --tags Environment=Production Service=Payment

# Agregar connection string (multiline)
az keyvault secret set \
  --vault-name $VAULT_NAME \
  --name "sql-connection-string" \
  --value "Server=tcp:myserver.database.windows.net,1433;Database=mydb;User ID=admin;Password=P@ssw0rd;Encrypt=True;" \
  --content-type "application/x-connection-string"

# Agregar JSON como secret
az keyvault secret set \
  --vault-name $VAULT_NAME \
  --name "service-account-json" \
  --file service-account.json \
  --content-type "application/json"
```

**Claves Criptográficas:**
```bash
# Crear clave RSA software-protected
az keyvault key create \
  --vault-name $VAULT_NAME \
  --name "data-encryption-key" \
  --kty RSA \
  --size 4096 \
  --protection software \
  --ops encrypt decrypt wrapKey unwrapKey

# Crear clave EC (Elliptic Curve) para firma
az keyvault key create \
  --vault-name $VAULT_NAME \
  --name "signing-key" \
  --kty EC \
  --curve P-256 \
  --protection software \
  --ops sign verify

# Crear clave HSM-protected (requiere Premium tier)
az keyvault key create \
  --vault-name kv-myapp-prod-hsm \
  --name "master-key-hsm" \
  --kty RSA-HSM \
  --size 4096 \
  --protection hsm

# Importar clave existente (BYOK)
az keyvault key import \
  --vault-name $VAULT_NAME \
  --name "imported-key" \
  --pem-file my-key.pem
```

**Certificados:**
```bash
# Crear self-signed certificate (desarrollo)
az keyvault certificate create \
  --vault-name $VAULT_NAME \
  --name "dev-cert" \
  --policy @- <<EOF
{
  "issuerParameters": {
    "name": "Self"
  },
  "x509CertificateProperties": {
    "subject": "CN=myapp.dev.local",
    "validityInMonths": 12,
    "keyUsage": [
      "digitalSignature",
      "keyEncipherment"
    ],
    "extendedKeyUsage": [
      "1.3.6.1.5.5.7.3.1",
      "1.3.6.1.5.5.7.3.2"
    ]
  },
  "keyProperties": {
    "keyType": "RSA",
    "keySize": 2048,
    "exportable": true,
    "reuseKey": false
  },
  "secretProperties": {
    "contentType": "application/x-pkcs12"
  }
}
EOF

# Importar certificado existente
az keyvault certificate import \
  --vault-name $VAULT_NAME \
  --name "prod-wildcard-cert" \
  --file wildcard.pfx \
  --password "CertPassword123!"

# Configurar auto-renovación con DigiCert
az keyvault certificate issuer create \
  --vault-name $VAULT_NAME \
  --issuer-name DigiCertIssuer \
  --provider-name DigiCert \
  --account-id "12345" \
  --password "ApiKey"

az keyvault certificate create \
  --vault-name $VAULT_NAME \
  --name "prod-cert-autorenew" \
  --policy @- <<EOF
{
  "issuerParameters": {
    "name": "DigiCertIssuer"
  },
  "x509CertificateProperties": {
    "subject": "CN=api.empresa.com",
    "validityInMonths": 12
  },
  "lifetimeActions": [
    {
      "trigger": {
        "daysBeforeExpiry": 30
      },
      "action": {
        "actionType": "AutoRenew"
      }
    }
  ]
}
EOF
```

#### Paso 3: Configurar Managed Identity

**Para Azure App Service / Functions:**
```bash
# Habilitar System-Assigned Managed Identity
az webapp identity assign \
  --name myapp \
  --resource-group $RESOURCE_GROUP

# Obtener el Principal ID
PRINCIPAL_ID=$(az webapp identity show \
  --name myapp \
  --resource-group $RESOURCE_GROUP \
  --query principalId -o tsv)

# Dar permisos RBAC
az role assignment create \
  --role "Key Vault Secrets User" \
  --assignee $PRINCIPAL_ID \
  --scope $(az keyvault show --name $VAULT_NAME --query id -o tsv)
```

**Para AKS (Workload Identity):**
```bash
# Crear Managed Identity
az identity create \
  --name mi-aks-workload \
  --resource-group $RESOURCE_GROUP

MI_CLIENT_ID=$(az identity show \
  --name mi-aks-workload \
  --resource-group $RESOURCE_GROUP \
  --query clientId -o tsv)

MI_PRINCIPAL_ID=$(az identity show \
  --name mi-aks-workload \
  --resource-group $RESOURCE_GROUP \
  --query principalId -o tsv)

# Dar permisos al Key Vault
az role assignment create \
  --role "Key Vault Secrets User" \
  --assignee $MI_PRINCIPAL_ID \
  --scope $(az keyvault show --name $VAULT_NAME --query id -o tsv)

# Crear Federated Credential para AKS
az identity federated-credential create \
  --name aks-federated-credential \
  --identity-name mi-aks-workload \
  --resource-group $RESOURCE_GROUP \
  --issuer $(az aks show --name myaks --resource-group $RESOURCE_GROUP --query oidcIssuerProfile.issuerUrl -o tsv) \
  --subject system:serviceaccount:default:workload-sa
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
    
    <!-- Azure Key Vault Secrets Spring Boot Starter -->
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>spring-cloud-azure-starter-keyvault-secrets</artifactId>
        <version>5.8.0</version>
    </dependency>
    
    <!-- Azure Identity (para Managed Identity) -->
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.11.0</version>
    </dependency>
    
    <!-- Para usar @Value con Key Vault -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

#### 2. Configuración

**application.yml:**
```yaml
spring:
  application:
    name: employee-api
  
  # Azure Key Vault
  cloud:
    azure:
      keyvault:
        secret:
          endpoint: https://kv-myapp-prod-001.vault.azure.net/
          # Usa Managed Identity automáticamente si está disponible
          # credential:
          #   managed-identity-enabled: true
  
  # Datasource usando secretos de Key Vault
  datasource:
    url: ${sql-connection-string}
    username: ${database-username}
    password: ${database-password}
    driver-class-name: org.postgresql.Driver

# Configuración de logging
logging:
  level:
    com.azure: DEBUG
    com.empresa: INFO

# Actuator para health checks
management:
  endpoints:
    web:
      exposure:
        include: health,info
```

**application-local.yml (para desarrollo local):**
```yaml
spring:
  cloud:
    azure:
      keyvault:
        secret:
          endpoint: https://kv-myapp-dev.vault.azure.net/
          # Para local, usar Azure CLI authentication
          credential:
            managed-identity-enabled: false
  
  # Override para local (no usar Key Vault)
  datasource:
    url: jdbc:postgresql://localhost:5432/employees
    username: postgres
    password: postgres
```

#### 3. Código de la Aplicación

**Acceso directo a Key Vault:**
```java
@Service
public class KeyVaultService {
    
    @Autowired
    private SecretClient secretClient;
    
    public String getSecret(String secretName) {
        KeyVaultSecret secret = secretClient.getSecret(secretName);
        return secret.getValue();
    }
    
    public void setSecret(String secretName, String value) {
        secretClient.setSecret(secretName, value);
    }
    
    public Map<String, String> getAllSecrets() {
        Map<String, String> secrets = new HashMap<>();
        
        secretClient.listPropertiesOfSecrets().forEach(secretProperties -> {
            KeyVaultSecret secret = secretClient.getSecret(secretProperties.getName());
            secrets.put(secret.getName(), secret.getValue());
        });
        
        return secrets;
    }
}
```

**Usando @Value con PropertySource:**
```java
@Service
public class PaymentService {
    
    // Inyecta automáticamente desde Key Vault
    @Value("${stripe-api-key}")
    private String stripeApiKey;
    
    @Value("${database-password}")
    private String databasePassword;
    
    public void processPayment(Payment payment) {
        // Usa stripeApiKey sin hard-coding
        Stripe.apiKey = stripeApiKey;
        
        try {
            Charge charge = Charge.create(chargeParams);
            log.info("Payment processed: {}", charge.getId());
        } catch (StripeException e) {
            log.error("Payment failed", e);
        }
    }
}
```

**Cache de secretos (reducir llamadas a Key Vault):**
```java
@Configuration
@EnableCaching
public class CacheConfig {
    
    @Bean
    public CacheManager cacheManager() {
        SimpleCacheManager cacheManager = new SimpleCacheManager();
        cacheManager.setCaches(Arrays.asList(
            new ConcurrentMapCache("secrets", 
                ConcurrentHashMap.newKeySet(), 
                false)
        ));
        return cacheManager;
    }
}

@Service
public class CachedSecretService {
    
    @Autowired
    private SecretClient secretClient;
    
    @Cacheable(value = "secrets", key = "#secretName")
    public String getSecret(String secretName) {
        log.info("Fetching secret from Key Vault: {}", secretName);
        return secretClient.getSecret(secretName).getValue();
    }
    
    @CacheEvict(value = "secrets", key = "#secretName")
    public void refreshSecret(String secretName) {
        log.info("Refreshing secret: {}", secretName);
    }
    
    @Scheduled(fixedRate = 300000) // 5 minutos
    @CacheEvict(value = "secrets", allEntries = true)
    public void refreshAllSecrets() {
        log.info("Refreshing all secrets cache");
    }
}
```

**Configuración programática con DefaultAzureCredential:**
```java
@Configuration
public class KeyVaultConfig {
    
    @Value("${spring.cloud.azure.keyvault.secret.endpoint}")
    private String keyVaultUri;
    
    @Bean
    public SecretClient secretClient() {
        // DefaultAzureCredential intenta en orden:
        // 1. Environment variables (AZURE_CLIENT_ID, etc.)
        // 2. Managed Identity
        // 3. Azure CLI
        // 4. IntelliJ/VS Code
        return new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();
    }
    
    // Para operaciones criptográficas
    @Bean
    public CryptographyClient cryptographyClient() {
        KeyClient keyClient = new KeyClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();
        
        KeyVaultKey key = keyClient.getKey("data-encryption-key");
        
        return new CryptographyClientBuilder()
            .credential(new DefaultAzureCredentialBuilder().build())
            .keyIdentifier(key.getId())
            .buildClient();
    }
}
```

**Cifrado/Descifrado con Key Vault:**
```java
@Service
public class EncryptionService {
    
    @Autowired
    private CryptographyClient cryptoClient;
    
    public String encrypt(String plaintext) {
        byte[] plaintextBytes = plaintext.getBytes(StandardCharsets.UTF_8);
        
        EncryptResult encryptResult = cryptoClient.encrypt(
            EncryptionAlgorithm.RSA_OAEP_256, 
            plaintextBytes
        );
        
        return Base64.getEncoder().encodeToString(encryptResult.getCipherText());
    }
    
    public String decrypt(String ciphertext) {
        byte[] ciphertextBytes = Base64.getDecoder().decode(ciphertext);
        
        DecryptResult decryptResult = cryptoClient.decrypt(
            EncryptionAlgorithm.RSA_OAEP_256, 
            ciphertextBytes
        );
        
        return new String(decryptResult.getPlainText(), StandardCharsets.UTF_8);
    }
}
```

#### 4. App Service Configuration

**Habilitar referencia a Key Vault en App Settings:**
```bash
# Configurar App Service para referenciar Key Vault
az webapp config appsettings set \
  --name myapp \
  --resource-group $RESOURCE_GROUP \
  --settings \
    "DATABASE_PASSWORD=@Microsoft.KeyVault(SecretUri=https://kv-myapp-prod-001.vault.azure.net/secrets/database-password/)" \
    "STRIPE_API_KEY=@Microsoft.KeyVault(SecretUri=https://kv-myapp-prod-001.vault.azure.net/secrets/stripe-api-key/)"
```

### 💻 Ejemplo Completo con Quarkus

#### 1. Dependencias Maven

**pom.xml:**
```xml
<dependencies>
    <!-- Quarkus REST -->
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-resteasy-reactive-jackson</artifactId>
    </dependency>
    
    <!-- Azure Key Vault extension -->
    <dependency>
        <groupId>io.quarkiverse.azureservices</groupId>
        <artifactId>quarkus-azure-keyvault</artifactId>
        <version>1.0.0</version>
    </dependency>
    
    <!-- Azure Identity -->
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.11.0</version>
    </dependency>
</dependencies>
```

#### 2. Configuración

**application.properties:**
```properties
# Application
quarkus.application.name=employee-api

# Azure Key Vault
azure.keyvault.url=https://kv-myapp-prod-001.vault.azure.net/
azure.keyvault.credential-type=managed-identity

# Datasource usando config from Key Vault
quarkus.datasource.jdbc.url=${kv.sql-connection-string}
quarkus.datasource.username=${kv.database-username}
quarkus.datasource.password=${kv.database-password}

# Logging
quarkus.log.category."com.azure".level=DEBUG
```

#### 3. Código de la Aplicación

**Producer para SecretClient:**
```java
@ApplicationScoped
public class KeyVaultProducer {
    
    @ConfigProperty(name = "azure.keyvault.url")
    String keyVaultUrl;
    
    @Produces
    @ApplicationScoped
    public SecretClient secretClient() {
        return new SecretClientBuilder()
            .vaultUrl(keyVaultUrl)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();
    }
}
```

**Servicio para acceder a secretos:**
```java
@ApplicationScoped
public class SecretService {
    
    @Inject
    SecretClient secretClient;
    
    @CacheResult(cacheName = "secrets")
    public String getSecret(String secretName) {
        Log.infof("Fetching secret: %s", secretName);
        return secretClient.getSecret(secretName).getValue();
    }
    
    public void setSecret(String secretName, String value) {
        secretClient.setSecret(secretName, value);
        Log.infof("Secret set: %s", secretName);
    }
}
```

**REST Endpoint:**
```java
@Path("/secrets")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class SecretResource {
    
    @Inject
    SecretService secretService;
    
    @GET
    @Path("/{name}")
    @RolesAllowed("admin")
    public String getSecret(@PathParam("name") String name) {
        return secretService.getSecret(name);
    }
    
    @POST
    @RolesAllowed("admin")
    public Response setSecret(
        @QueryParam("name") String name,
        String value
    ) {
        secretService.setSecret(name, value);
        return Response.ok().build();
    }
}
```

### 📊 Comparación: Key Vault vs Alternativas

| Característica | Azure Key Vault | HashiCorp Vault | AWS Secrets Manager | Kubernetes Secrets |
|----------------|-----------------|-----------------|---------------------|-------------------|
| **Managed** | Completamente | Self-hosted | Completamente | Integrado en K8s |
| **Costo** | $0.03/10k ops | Licencia + infra | $0.40/secret/mes | Gratis |
| **HSM** | Tier Premium | Enterprise | Sí | No |
| **Portabilidad** | Solo Azure | Multi-cloud | Solo AWS | Cualquier K8s |
| **Rotación auto** | Limitado | Avanzado | Sí | Manual |
| **Auditoría** | Azure Monitor | Audit logs | CloudTrail | Audit logs |
| **Integración** | Azure services | APIs/Agents | AWS services | Pods nativamente |
| **Complejidad** | Baja | Alta | Baja | Media |
| **Latencia** | ~50ms | Variable | ~50ms | <1ms (in-memory) |
| **Versioning** | Sí | Sí | Sí | Limitado |

### 🔍 Troubleshooting Común

**Problema 1: Forbidden (403) al acceder a secret**
```bash
# Verificar permisos RBAC
az role assignment list \
  --assignee <principal-id> \
  --scope $(az keyvault show --name $VAULT_NAME --query id -o tsv)

# Verificar si Managed Identity está habilitada
az webapp identity show --name myapp --resource-group $RESOURCE_GROUP

# Ver audit logs
az monitor diagnostic-settings list \
  --resource $(az keyvault show --name $VAULT_NAME --query id -o tsv)
```

**Problema 2: Throttling (429 Too Many Requests)**
```bash
# Ver métricas de uso
az monitor metrics list \
  --resource $(az keyvault show --name $VAULT_NAME --query id -o tsv) \
  --metric ServiceApiResult \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-01-01T23:59:59Z

# Solución: Implementar cache en la aplicación
# Reducir frecuencia de lectura (no leer en cada request)
```

**Problema 3: Private Endpoint no resuelve DNS**
```bash
# Verificar DNS desde VM en la VNet
nslookup kv-myapp-prod-001.vault.azure.net

# Debería resolver a IP privada (10.x.x.x), no pública

# Si resuelve a IP pública, verificar Private DNS Zone
az network private-dns zone show \
  --resource-group $RESOURCE_GROUP \
  --name privatelink.vaultcore.azure.net
```

---

Esta es la guía completa de Azure Key Vault. ¿Continúo con **Azure Redis Cache**? 🔴