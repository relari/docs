# 🚢 Azure Kubernetes Service (AKS) - Guía Completa

## 📖 Definición Detallada

Azure Kubernetes Service (AKS) es un **servicio de orquestación de contenedores completamente administrado** que simplifica el despliegue, gestión y operación de clústeres de Kubernetes en Azure. Kubernetes es un sistema open-source para automatizar el despliegue, escalado y gestión de aplicaciones en contenedores.

**Componentes arquitectónicos:**

**Control Plane (Gestionado por Azure - GRATIS):**
- **API Server:** Punto de entrada para todas las operaciones del clúster
- **etcd:** Base de datos distribuida que almacena la configuración del clúster
- **Scheduler:** Decide en qué nodos ejecutar los pods
- **Controller Manager:** Ejecuta controladores que regulan el estado del clúster
- **Cloud Controller Manager:** Integración específica con Azure

**Data Plane (Gestionado por el usuario - PAGAS POR VMs):**
- **Nodes (Worker Nodes):** VMs que ejecutan los contenedores
- **Kubelet:** Agente que se ejecuta en cada nodo
- **Kube-proxy:** Gestiona reglas de red en los nodos
- **Container Runtime:** Docker, containerd o CRI-O

**Objetos de Kubernetes:**
- **Pod:** Unidad mínima de despliegue (1+ contenedores)
- **Deployment:** Gestiona réplicas de pods
- **Service:** Expone pods con una IP estable
- **Ingress:** Enrutamiento HTTP/HTTPS a servicios
- **ConfigMap/Secret:** Configuración y credenciales
- **StatefulSet:** Para aplicaciones con estado (bases de datos)
- **DaemonSet:** Ejecuta un pod en cada nodo
- **Job/CronJob:** Tareas batch y programadas

### 🎯 Importancia

**Por qué AKS es crítico en arquitecturas modernas:**

1. **Microservicios a escala:** Gestiona cientos de servicios independientes
2. **Alta disponibilidad:** Recuperación automática de fallos, multi-zona
3. **Escalabilidad dinámica:** Horizontal (más pods) y vertical (más recursos por pod)
4. **Portabilidad:** Kubernetes es el estándar, migración entre clouds
5. **Eficiencia de recursos:** Packing inteligente de contenedores en VMs
6. **DevOps:** Integración con CI/CD, GitOps, infraestructura como código
7. **Observabilidad:** Integración nativa con Azure Monitor, Prometheus, Grafana
8. **Seguridad:** Network policies, RBAC, Azure AD integration, Pod Security Standards

### ✨ Características Principales

**1. Gestión del Control Plane:**
- ✅ Azure gestiona completamente (patching, updates, HA)
- ✅ SLA 99.95% con Availability Zones
- ✅ Sin costo por el control plane
- ✅ Backups automáticos de etcd

**2. Node Pools:**
- Múltiples pools con diferentes tipos de VM
- Windows y Linux nodes en el mismo clúster
- Spot VMs para workloads tolerantes a interrupciones
- Escalado automático por pool (Cluster Autoscaler)

**3. Networking:**
- **Azure CNI:** Pods obtienen IPs del VNet (integración profunda)
- **Kubenet:** Pods usan IPs privadas con NAT (más económico)
- **CNI Overlay:** Pods en red overlay separada
- Network Policies (Calico, Azure Network Policy)
- Application Gateway Ingress Controller (AGIC)
- Azure Load Balancer integrado

**4. Storage:**
- Azure Disks (Premium SSD, Standard SSD, Standard HDD)
- Azure Files (SMB shares para múltiples pods)
- Azure NetApp Files (NFS de alto rendimiento)
- Storage Classes dinámicas
- Persistent Volumes con reclaim policies

**5. Seguridad:**
- Azure AD integration para RBAC
- Pod Identity / Workload Identity (sin service principals)
- Azure Key Vault integration para secrets
- Pod Security Standards (Restricted, Baseline, Privileged)
- Network Policies para microsegmentación
- Azure Defender for Containers

**6. Observabilidad:**
- Container Insights (métricas y logs)
- Prometheus y Grafana
- Azure Monitor integration
- Live logs y exec en pods
- Distributed tracing con Application Insights

**7. Add-ons y extensiones:**
- **Azure Monitor:** Telemetría
- **Azure Policy:** Compliance y governance
- **HTTP Application Routing:** Ingress simplificado
- **Open Service Mesh:** Service mesh
- **KEDA:** Event-driven autoscaling
- **Dapr:** Building blocks para microservicios
- **GitOps con Flux:** Despliegue declarativo

**8. Upgrades y mantenimiento:**
- Ventanas de mantenimiento programadas
- Node surge durante upgrades
- Upgrade automático o manual
- Canales de release (rapid, stable, patch)

### ✅ Ventajas

1. **Control Plane gestionado GRATIS:** Solo pagas por los nodes (VMs)
2. **Integración profunda con Azure:** VNet, Load Balancer, Azure AD, Key Vault
3. **Escalabilidad masiva:** Hasta 5,000 nodes y 200,000 pods por clúster
4. **Multi-tenancy:** Múltiples equipos/aplicaciones en un clúster con namespaces
5. **Ecosistema maduro:** Helm, Operators, service meshes (Istio, Linkerd)
6. **Disaster recovery:** Backup de aplicaciones con Velero
7. **Desarrollo local:** Minikube, Kind, Docker Desktop para replicar el ambiente
8. **GitOps:** Infraestructura como código con ArgoCD o Flux
9. **Cost optimization:** Spot VMs, auto-scaling, shutdown schedules
10. **Compliance:** Cumple con SOC, ISO, HIPAA, PCI-DSS

### ❌ Desventajas

1. **Complejidad operacional:** Curva de aprendizaje pronunciada
2. **Overhead de recursos:** Kubernetes consume CPU/RAM (kubelet, kube-proxy, etc.)
3. **Costo de VMs:** Los nodes pueden ser caros (D-series, E-series)
4. **Networking complejo:** CNI, Network Policies, Ingress, Service Mesh
5. **Debugging difícil:** Problemas distribuidos son complejos de diagnosticar
6. **Storage stateful:** Gestionar bases de datos en K8s es desafiante
7. **Actualizaciones disruptivas:** Upgrades pueden causar downtime si no se planean
8. **Lock-in parcial:** AKS-specific features (AGIC, Azure CNI) dificultan migración
9. **Seguridad compleja:** RBAC, Network Policies, Pod Security requieren expertise
10. **Overkill para apps simples:** Para 1-2 microservicios, App Service es más simple

### 🎯 Cuándo Aplicarlo

**✅ USAR AKS cuando:**

1. **Arquitectura de microservicios:** 10+ servicios independientes
2. **Alta disponibilidad crítica:** Necesitas self-healing y multi-zona
3. **Escalabilidad horizontal:** Tráfico variable con picos impredecibles
4. **Portabilidad multi-cloud:** Plan de migración o híbrido (Azure + AWS)
5. **DevOps maduro:** CI/CD automatizado, GitOps, infraestructura como código
6. **Aplicaciones stateless:** APIs REST, workers de procesamiento
7. **Workloads batch:** Jobs programados o event-driven (KEDA)
8. **Machine Learning:** Kubeflow, MLflow para entrenar modelos a escala
9. **Multi-tenancy:** Múltiples equipos compartiendo infraestructura
10. **Regulación estricta:** Necesitas control granular de red y seguridad

**Ejemplos específicos:**
- E-commerce con 50+ microservicios
- Plataforma SaaS multi-tenant
- API Gateway con backends escalables
- Sistema de procesamiento de pagos con alta disponibilidad
- Plataforma de streaming con picos de tráfico

**❌ NO USAR AKS cuando:**

1. **Aplicación monolítica simple:** 1-2 servicios → Usa Azure App Service
2. **Equipo sin experiencia en K8s:** Curva de aprendizaje muy pronunciada
3. **Presupuesto limitado:** VMs 24/7 son costosas → Considera Container Apps
4. **Workload serverless puro:** Usa Azure Functions o Logic Apps
5. **Bases de datos críticas:** No corras SQL Server, PostgreSQL en K8s → Usa Azure SQL, Cosmos
6. **Windows legacy apps:** Aplicaciones .NET Framework antiguasµ → App Service o VMs
7. **Latencia ultra-baja crítica:** El overhead de networking puede ser problema
8. **Apps con estado complejo:** Sistemas legacy con almacenamiento local
9. **Cumplimiento que prohíbe contenedores:** Algunas regulaciones legacy
10. **Proyectos cortos (< 6 meses):** El ROI no justifica la inversión en aprendizaje

### 📋 Situaciones Específicas

**Escenario 1: Startup con 3 microservicios**
- ❌ AKS: Overhead innecesario
- ✅ Alternativa: Azure Container Apps (serverless + Kubernetes compatible)

**Escenario 2: Empresa con 100+ microservicios**
- ✅ AKS: Ideal para gestión centralizada y escalabilidad

**Escenario 3: Aplicación que necesita PCI-DSS compliance**
- ✅ AKS: Network Policies, Pod Security Standards, Azure Defender

**Escenario 4: Base de datos PostgreSQL crítica**
- ❌ No en AKS: Usa Azure Database for PostgreSQL
- ⚠️ Excepción: PostgreSQL operator (CrunchyData) para casos avanzados

**Escenario 5: ML Training a escala**
- ✅ AKS: Con GPU nodes (NC-series, ND-series) + Kubeflow

**Escenario 6: Blog en WordPress**
- ❌ AKS: Totalmente overkill
- ✅ Alternativa: Azure App Service for Containers

**Escenario 7: Sistema batch que procesa archivos cada noche**
- ⚠️ AKS con CronJobs: Funciona pero costoso
- ✅ Mejor: Azure Batch o Azure Functions con timer trigger

**Escenario 8: Multi-cloud con AWS y Azure**
- ✅ AKS + EKS: Kubernetes facilita portabilidad

### 🔧 Políticas y Mejores Prácticas

#### 1. Resource Quotas y Limits

**Namespace Quota (evita noisy neighbors):**
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: production
spec:
  hard:
    requests.cpu: "100"
    requests.memory: 200Gi
    limits.cpu: "200"
    limits.memory: 400Gi
    persistentvolumeclaims: "10"
    pods: "50"
```

**LimitRange (valores default por pod):**
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
  namespace: production
spec:
  limits:
  - default:           # Limits por defecto
      cpu: 500m
      memory: 512Mi
    defaultRequest:    # Requests por defecto
      cpu: 200m
      memory: 256Mi
    max:               # Máximo permitido
      cpu: 2
      memory: 4Gi
    min:               # Mínimo requerido
      cpu: 100m
      memory: 64Mi
    type: Container
```

#### 2. Pod Security Standards

**Política restrictiva (producción):**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted
```

#### 3. Network Policies

**Deny all por defecto:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

**Permitir solo tráfico necesario:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-api-to-database
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: database
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: api
    ports:
    - protocol: TCP
      port: 5432
```

#### 4. RBAC (Role-Based Access Control)

**Role para desarrolladores (solo lectura):**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: developer-role
rules:
- apiGroups: ["", "apps", "batch"]
  resources: ["pods", "pods/log", "deployments", "services"]
  verbs: ["get", "list", "watch"]
```

**RoleBinding:**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding
  namespace: development
subjects:
- kind: Group
  name: "developers@empresa.com"
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer-role
  apiGroup: rbac.authorization.k8s.io
```

#### 5. Pod Disruption Budgets (Alta disponibilidad)

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: api-pdb
spec:
  minAvailable: 2  # Siempre mínimo 2 pods disponibles
  selector:
    matchLabels:
      app: api
```

### 📦 Configuración Paso a Paso

#### Paso 1: Crear el clúster AKS

**Opción A: Azure CLI (básico)**
```bash
# Variables
RESOURCE_GROUP="rg-aks-prod"
LOCATION="eastus"
CLUSTER_NAME="aks-empresa-prod"
NODE_COUNT=3
NODE_SIZE="Standard_D4s_v3"

# Crear resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# Crear AKS con configuración básica
az aks create \
  --resource-group $RESOURCE_GROUP \
  --name $CLUSTER_NAME \
  --location $LOCATION \
  --node-count $NODE_COUNT \
  --node-vm-size $NODE_SIZE \
  --enable-managed-identity \
  --generate-ssh-keys \
  --network-plugin azure \
  --service-cidr 10.0.0.0/16 \
  --dns-service-ip 10.0.0.10 \
  --enable-addons monitoring \
  --zones 1 2 3

# Tarda ~10 minutos
```

**Opción B: Azure CLI (producción completa)**
```bash
# Variables adicionales
VNET_NAME="vnet-aks"
SUBNET_NAME="subnet-aks-nodes"
VNET_CIDR="10.1.0.0/16"
SUBNET_CIDR="10.1.0.0/20"
ACR_NAME="mycompanyacr"

# Crear VNet
az network vnet create \
  --resource-group $RESOURCE_GROUP \
  --name $VNET_NAME \
  --address-prefixes $VNET_CIDR \
  --subnet-name $SUBNET_NAME \
  --subnet-prefixes $SUBNET_CIDR

SUBNET_ID=$(az network vnet subnet show \
  --resource-group $RESOURCE_GROUP \
  --vnet-name $VNET_NAME \
  --name $SUBNET_NAME \
  --query id -o tsv)

# Crear Azure Container Registry
az acr create \
  --resource-group $RESOURCE_GROUP \
  --name $ACR_NAME \
  --sku Standard

# Crear AKS con configuración de producción
az aks create \
  --resource-group $RESOURCE_GROUP \
  --name $CLUSTER_NAME \
  --location $LOCATION \
  --kubernetes-version 1.28.3 \
  --node-count 3 \
  --min-count 3 \
  --max-count 10 \
  --node-vm-size Standard_D4s_v3 \
  --node-osdisk-size 128 \
  --node-osdisk-type Managed \
  --enable-cluster-autoscaler \
  --enable-managed-identity \
  --network-plugin azure \
  --network-policy azure \
  --vnet-subnet-id $SUBNET_ID \
  --service-cidr 10.0.0.0/16 \
  --dns-service-ip 10.0.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --enable-addons monitoring,azure-policy \
  --zones 1 2 3 \
  --tier standard \
  --uptime-sla \
  --attach-acr $ACR_NAME \
  --enable-oidc-issuer \
  --enable-workload-identity

# Obtener credenciales
az aks get-credentials \
  --resource-group $RESOURCE_GROUP \
  --name $CLUSTER_NAME \
  --overwrite-existing

# Verificar conexión
kubectl get nodes
```

**Opción C: Terraform (infraestructura como código)**
```hcl
# variables.tf
variable "resource_group_name" {
  default = "rg-aks-prod"
}

variable "location" {
  default = "eastus"
}

variable "cluster_name" {
  default = "aks-empresa-prod"
}

# main.tf
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet-aks"
  address_space       = ["10.1.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "aks_subnet" {
  name                 = "subnet-aks-nodes"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.1.0.0/20"]
}

resource "azurerm_log_analytics_workspace" "logs" {
  name                = "logs-aks"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}

resource "azurerm_kubernetes_cluster" "aks" {
  name                = var.cluster_name
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  dns_prefix          = "aks-empresa"
  kubernetes_version  = "1.28.3"
  sku_tier            = "Standard"

  default_node_pool {
    name                = "system"
    node_count          = 3
    vm_size             = "Standard_D4s_v3"
    os_disk_size_gb     = 128
    vnet_subnet_id      = azurerm_subnet.aks_subnet.id
    enable_auto_scaling = true
    min_count           = 3
    max_count           = 10
    zones               = ["1", "2", "3"]
    
    node_labels = {
      role = "system"
    }
  }

  identity {
    type = "SystemAssigned"
  }

  network_profile {
    network_plugin     = "azure"
    network_policy     = "azure"
    load_balancer_sku  = "standard"
    service_cidr       = "10.0.0.0/16"
    dns_service_ip     = "10.0.0.10"
    docker_bridge_cidr = "172.17.0.1/16"
  }

  oms_agent {
    log_analytics_workspace_id = azurerm_log_analytics_workspace.logs.id
  }

  azure_policy_enabled = true
  
  oidc_issuer_enabled       = true
  workload_identity_enabled = true

  tags = {
    Environment = "Production"
    ManagedBy   = "Terraform"
  }
}

# Node pool adicional para aplicaciones
resource "azurerm_kubernetes_cluster_node_pool" "apps" {
  name                  = "apps"
  kubernetes_cluster_id = azurerm_kubernetes_cluster.aks.id
  vm_size               = "Standard_D8s_v3"
  node_count            = 2
  enable_auto_scaling   = true
  min_count             = 2
  max_count             = 20
  zones                 = ["1", "2", "3"]
  vnet_subnet_id        = azurerm_subnet.aks_subnet.id
  
  node_labels = {
    role = "application"
  }
  
  node_taints = [
    "workload=application:NoSchedule"
  ]
}

# Node pool con GPU para ML
resource "azurerm_kubernetes_cluster_node_pool" "gpu" {
  name                  = "gpu"
  kubernetes_cluster_id = azurerm_kubernetes_cluster.aks.id
  vm_size               = "Standard_NC6s_v3"
  node_count            = 0
  enable_auto_scaling   = true
  min_count             = 0
  max_count             = 5
  vnet_subnet_id        = azurerm_subnet.aks_subnet.id
  
  node_labels = {
    role                         = "gpu"
    "nvidia.com/gpu"             = "true"
    "accelerator"                = "nvidia"
  }
  
  node_taints = [
    "sku=gpu:NoSchedule"
  ]
}

# outputs.tf
output "kube_config" {
  value     = azurerm_kubernetes_cluster.aks.kube_config_raw
  sensitive = true
}

output "cluster_name" {
  value = azurerm_kubernetes_cluster.aks.name
}

output "resource_group_name" {
  value = azurerm_resource_group.rg.name
}
```

```bash
# Desplegar con Terraform
terraform init
terraform plan
terraform apply -auto-approve

# Obtener kubeconfig
az aks get-credentials \
  --resource-group $(terraform output -raw resource_group_name) \
  --name $(terraform output -raw cluster_name)
```

#### Paso 2: Configurar Ingress Controller (NGINX)

```bash
# Agregar Helm repo
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

# Instalar NGINX Ingress Controller
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --create-namespace \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz \
  --set controller.replicaCount=3 \
  --set controller.nodeSelector."kubernetes\.io/os"=linux \
  --set controller.resources.requests.cpu=100m \
  --set controller.resources.requests.memory=128Mi \
  --set controller.autoscaling.enabled=true \
  --set controller.autoscaling.minReplicas=3 \
  --set controller.autoscaling.maxReplicas=10

# Obtener IP pública del LoadBalancer
kubectl get service ingress-nginx-controller -n ingress-nginx

# Configurar DNS A record apuntando a esta IP
# api.empresa.com -> <EXTERNAL-IP>
```

#### Paso 3: Instalar Cert-Manager (SSL automático)

```bash
# Instalar cert-manager
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.0/cert-manager.yaml

# Esperar a que esté listo
kubectl wait --for=condition=ready pod -l app.kubernetes.io/instance=cert-manager -n cert-manager --timeout=90s

# ClusterIssuer para Let's Encrypt
cat <<EOF | kubectl apply -f -
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: admin@empresa.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
EOF
```

#### Paso 4: Configurar Azure Key Vault Secrets Store CSI Driver

```bash
# Habilitar addon
az aks enable-addons \
  --resource-group $RESOURCE_GROUP \
  --name $CLUSTER_NAME \
  --addons azure-keyvault-secrets-provider

# Crear Key Vault
az keyvault create \
  --name kv-aks-secrets \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION

# Dar permisos al cluster identity
CLUSTER_IDENTITY=$(az aks show \
  --resource-group $RESOURCE_GROUP \
  --name $CLUSTER_NAME \
  --query identityProfile.kubeletidentity.clientId -o tsv)

az keyvault set-policy \
  --name kv-aks-secrets \
  --spn $CLUSTER_IDENTITY \
  --secret-permissions get list

# Agregar secret al Key Vault
az keyvault secret set \
  --vault-name kv-aks-secrets \
  --name database-password \
  --value "SuperSecretPassword123!"
```

### 💻 Ejemplo Completo con Spring Boot

#### 1. Aplicación Spring Boot

**Dockerfile multi-stage:**
```dockerfile
# Build stage
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Runtime stage
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar

# Non-root user
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s \
  CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**application.yml con configuración para K8s:**
```yaml
spring:
  application:
    name: employee-api
  
  # Datasource desde secret
  datasource:
    url: jdbc:postgresql://${DB_HOST:postgres}:${DB_PORT:5432}/${DB_NAME:employees}
    username: ${DB_USER:postgres}
    password: ${DB_PASSWORD}
    
  # Configuración para health checks
  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: false

# Management endpoints para K8s probes
management:
  endpoints:
    web:
      exposure:
        include: health,info,prometheus
  endpoint:
    health:
      probes:
        enabled: true
      show-details: always
  metrics:
    export:
      prometheus:
        enabled: true

# Logging
logging:
  level:
    root: INFO
    com.empresa: DEBUG
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"

# Server
server:
  port: 8080
  shutdown: graceful
  tomcat:
    connection-timeout: 20s

# Graceful shutdown
spring.lifecycle.timeout-per-shutdown-phase: 30s
```

#### 2. Kubernetes Manifests

**namespace.yaml:**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    name: production
    pod-security.kubernetes.io/enforce: restricted
```

**secret.yaml (usando Azure Key Vault):**
```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-keyvault-secrets
  namespace: production
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"
    userAssignedIdentityID: ""
    keyvaultName: "kv-aks-secrets"
    tenantId: "your-tenant-id"
    objects: |
      array:
        - |
          objectName: database-password
          objectType: secret
          objectVersion: ""
    secretObjects:
    - secretName: db-credentials
      type: Opaque
      data:
      - objectName: database-password
        key: password
```

**configmap.yaml:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: employee-api-config
  namespace: production
data:
  DB_HOST: "postgres.production.svc.cluster.local"
  DB_PORT: "5432"
  DB_NAME: "employees"
  DB_USER: "apiuser"
  SPRING_PROFILES_ACTIVE: "production"
```

**deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: employee-api
  namespace: production
  labels:
    app: employee-api
    version: v1
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: employee-api
  template:
    metadata:
      labels:
        app: employee-api
        version: v1
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
        prometheus.io/path: "/actuator/prometheus"
    spec:
      # Toleration para node pool específico
      tolerations:
      - key: "workload"
        operator: "Equal"
        value: "application"
        effect: "NoSchedule"
      
      # Node selector
      nodeSelector:
        role: application
      
      # Affinity para distribuir pods en diferentes zonas
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app: employee-api
              topologyKey: topology.kubernetes.io/zone
      
      # Service account
      serviceAccountName: employee-api-sa
      
      # Init container para esperar PostgreSQL
      initContainers:
      - name: wait-for-db
        image: busybox:1.36
        command: ['sh', '-c', 'until nc -z postgres 5432; do echo waiting for postgres; sleep 2; done;']
      
      containers:
      - name: employee-api
        image: mycompanyacr.azurecr.io/employee-api:1.0.0
        imagePullPolicy: Always
        
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
        
        # Environment from ConfigMap
        envFrom:
        - configMapRef:
            name: employee-api-config
        
        # Secrets from Azure Key Vault
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: password
        
        # Resources (CRÍTICO para scheduling)
        resources:
          requests:
            cpu: 500m
            memory: 512Mi
          limits:
            cpu: 1000m
            memory: 1Gi
        
        # Liveness probe (reinicia pod si falla)
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
          timeoutSeconds: 5
          failureThreshold: 3
        
        # Readiness probe (quita del servicio si no está listo)
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 5
          timeoutSeconds: 3
          failureThreshold: 3
        
        # Startup probe (para apps con startup lento)
        startupProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 0
          periodSeconds: 10
          timeoutSeconds: 3
          failureThreshold: 30
        
        # Security context
        securityContext:
          runAsNonRoot: true
          runAsUser: 1000
          allowPrivilegeEscalation: false
          capabilities:
            drop:
            - ALL
          readOnlyRootFilesystem: true
        
        # Volume mounts
        volumeMounts:
        - name: tmp
          mountPath: /tmp
        - name: secrets-store
          mountPath: "/mnt/secrets-store"
          readOnly: true
      
      volumes:
      - name: tmp
        emptyDir: {}
      - name: secrets-store
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "azure-keyvault-secrets"
```

**service.yaml:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: employee-api
  namespace: production
  labels:
    app: employee-api
spec:
  type: ClusterIP
  selector:
    app: employee-api
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 8080
  sessionAffinity: None
```

**ingress.yaml:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: employee-api
  namespace: production
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    nginx.ingress.kubernetes.io/rate-limit: "100"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - api.empresa.com
    secretName: api-tls-cert
  rules:
  - host: api.empresa.com
    http:
      paths:
      - path: /api/v1/employees
        pathType: Prefix
        backend:
          service:
            name: employee-api
            port:
              number: 80
```

**hpa.yaml (Horizontal Pod Autoscaler):**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: employee-api-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: employee-api
  minReplicas: 3
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
```

#### 3. CI/CD Pipeline (Azure DevOps)

**azure-pipelines.yml:**
```yaml
trigger:
  branches:
    include:
    - main
  paths:
    include:
    - src/*
    - pom.xml

variables:
  azureSubscription: 'Azure-Service-Connection'
  resourceGroup: 'rg-aks-prod'
  clusterName: 'aks-empresa-prod'
  acrName: 'mycompanyacr'
  imageName: 'employee-api'
  tag: '$(Build.BuildId)'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: Maven@3
      inputs:
        mavenPomFile: 'pom.xml'
        goals: 'clean package'
        options: '-DskipTests'
    
    - task: Docker@2
      displayName: 'Build Docker Image'
      inputs:
        command: 'build'
        repository: '$(imageName)'
        dockerfile: 'Dockerfile'
        tags: |
          $(tag)
          latest
    
    - task: AzureCLI@2
      displayName: 'Push to ACR'
      inputs:
        azureSubscription: $(azureSubscription)
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: |
          az acr login --name $(acrName)
          docker tag $(imageName):$(tag) $(acrName).azurecr.io/$(imageName):$(tag)
          docker tag $(imageName):latest $(acrName).azurecr.io/$(imageName):latest
          docker push $(acrName).azurecr.io/$(imageName):$(tag)
          docker push $(acrName).azurecr.io/$(imageName):latest

- stage: Deploy
  dependsOn: Build
  jobs:
  - deployment: DeployToAKS
    pool:
      vmImage: 'ubuntu-latest'
    environment: 'production'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: KubernetesManifest@0
            displayName: 'Deploy to AKS'
            inputs:
              action: 'deploy'
              kubernetesServiceConnection: 'aks-connection'
              namespace: 'production'
              manifests: |
                k8s/deployment.yaml
                k8s/service.yaml
                k8s/ingress.yaml
                k8s/hpa.yaml
              containers: |
                $(acrName).azurecr.io/$(imageName):$(tag)
```

### 💻 Ejemplo Completo con Quarkus

#### 1. Aplicación Quarkus

**Dockerfile.native (compilación nativa con GraalVM):**
```dockerfile
## Stage 1 : build with maven builder image
FROM quay.io/quarkus/ubi-quarkus-mandrel-builder-image:jdk-21 AS build
COPY --chown=quarkus:quarkus mvnw /code/mvnw
COPY --chown=quarkus:quarkus .mvn /code/.mvn
COPY --chown=quarkus:quarkus pom.xml /code/
USER quarkus
WORKDIR /code
RUN ./mvnw -B org.apache.maven.plugins:maven-dependency-plugin:3.6.1:go-offline
COPY src /code/src
RUN ./mvnw package -Pnative -DskipTests

## Stage 2 : create the docker final image
FROM quay.io/quarkus/quarkus-micro-image:2.0
WORKDIR /work/
COPY --from=build /code/target/*-runner /work/application

# Non-root user
RUN chown 1001 /work/application && chmod "g+rwX" /work && chown 1001:root /work
USER 1001

# Health check
HEALTHCHECK --interval=10s --timeout=3s --start-period=20s \
  CMD curl --fail http://localhost:8080/q/health/live || exit 1

EXPOSE 8080
CMD ["./application", "-Dquarkus.http.host=0.0.0.0"]
```

**application.properties:**
```properties
# Application
quarkus.application.name=employee-api
quarkus.http.port=8080
quarkus.http.root-path=/api

# Kubernetes native integration
quarkus.kubernetes.deployment-target=kubernetes
quarkus.kubernetes.namespace=production
quarkus.kubernetes.replicas=3
quarkus.kubernetes.image-pull-policy=always

# Resources
quarkus.kubernetes.resources.requests.memory=256Mi
quarkus.kubernetes.resources.requests.cpu=250m
quarkus.kubernetes.resources.limits.memory=512Mi
quarkus.kubernetes.resources.limits.cpu=500m

# Health & Liveness
quarkus.kubernetes.liveness-probe.http-action-path=/q/health/live
quarkus.kubernetes.liveness-probe.initial-delay=30s
quarkus.kubernetes.liveness-probe.period=10s

quarkus.kubernetes.readiness-probe.http-action-path=/q/health/ready
quarkus.kubernetes.readiness-probe.initial-delay=10s
quarkus.kubernetes.readiness-probe.period=5s

# Datasource (desde ConfigMap y Secret)
quarkus.datasource.jdbc.url=jdbc:postgresql://${DB_HOST:postgres}:${DB_PORT:5432}/${DB_NAME:employees}
quarkus.datasource.username=${DB_USER:postgres}
quarkus.datasource.password=${DB_PASSWORD}

# Hibernate
quarkus.hibernate-orm.database.generation=validate
quarkus.hibernate-orm.log.sql=false

# Metrics para Prometheus
quarkus.micrometer.export.prometheus.enabled=true
quarkus.micrometer.export.prometheus.path=/q/metrics

# Logging
quarkus.log.level=INFO
quarkus.log.category."com.empresa".level=DEBUG
quarkus.log.console.format=%d{yyyy-MM-dd HH:mm:ss} %-5p [%c{2.}] (%t) %s%e%n

# Graceful shutdown
quarkus.shutdown.timeout=30s
```

#### 2. Generar manifests con Quarkus Extension

```bash
# Agregar extensión de Kubernetes
./mvnw quarkus:add-extension -Dextensions="kubernetes"

# Compilar y generar manifests
./mvnw clean package -DskipTests

# Los manifests se generan automáticamente en:
# target/kubernetes/kubernetes.yml
```

**Manifest generado (simplificado):**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: employee-api
  namespace: production
spec:
  ports:
  - name: http
    port: 80
    targetPort: 8080
  selector:
    app.kubernetes.io/name: employee-api
  type: ClusterIP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: employee-api
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app.kubernetes.io/name: employee-api
  template:
    metadata:
      labels:
        app.kubernetes.io/name: employee-api
    spec:
      containers:
      - env:
        - name: KUBERNETES_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        envFrom:
        - configMapRef:
            name: employee-api-config
        - secretRef:
            name: db-credentials
        image: mycompanyacr.azurecr.io/employee-api:1.0.0
        imagePullPolicy: Always
        livenessProbe:
          httpGet:
            path: /q/health/live
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        name: employee-api
        ports:
        - containerPort: 8080
          name: http
          protocol: TCP
        readinessProbe:
          httpGet:
            path: /q/health/ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
        resources:
          limits:
            cpu: 500m
            memory: 512Mi
          requests:
            cpu: 250m
            memory: 256Mi
```

#### 3. Desplegar a AKS

```bash
# Build nativo y push a ACR
./mvnw clean package -Pnative -Dquarkus.native.container-build=true
docker build -f src/main/docker/Dockerfile.native -t mycompanyacr.azurecr.io/employee-api:1.0.0 .
az acr login --name mycompanyacr
docker push mycompanyacr.azurecr.io/employee-api:1.0.0

# Deploy
kubectl apply -f target/kubernetes/kubernetes.yml
kubectl apply -f k8s/ingress.yaml
kubectl apply -f k8s/hpa.yaml

# Verificar
kubectl get pods -n production -l app.kubernetes.io/name=employee-api
kubectl logs -f deployment/employee-api -n production
```

### 📊 Comparación: AKS vs Alternativas

| Característica | AKS | Azure Container Apps | Azure App Service | VMs |
|----------------|-----|----------------------|-------------------|-----|
| **Complejidad** | Alta | Baja | Muy Baja | Media |
| **Control** | Total | Medio | Bajo | Total |
| **Escalabilidad** | Masiva | Alta | Media | Manual |
| **Costo base** | VMs 24/7 | Serverless | Siempre activo | VMs 24/7 |
| **Portabilidad** | Alta (K8s) | Media | Baja | Alta |
| **Microservicios** | Excelente | Bueno | Limitado | Manual |
| **Learning curve** | Pronunciada | Suave | Mínima | Media |
| **Multi-cloud** | Sí | No | No | Sí |
| **Stateful apps** | Posible | Limitado | Sí | Sí |
| **DevOps maturity** | Requiere | Recomendado | Básico | Variable |

### 🔍 Troubleshooting Común

**Problema 1: Pods en estado CrashLoopBackOff**
```bash
# Ver logs
kubectl logs <pod-name> -n production

# Ver eventos
kubectl describe pod <pod-name> -n production

# Causas comunes:
# - Liveness probe falla muy pronto (aumentar initialDelaySeconds)
# - Sin recursos suficientes (revisar limits/requests)
# - Aplicación crashea al iniciar (revisar logs)
```

**Problema 2: Service no responde (timeout)**
```bash
# Verificar endpoints
kubectl get endpoints <service-name> -n production

# Si está vacío, los pods no pasan readiness probe
kubectl get pods -n production -l app=<app-name>

# Testear desde dentro del cluster
kubectl run debug --image=nicolaka/netshoot -it --rm -- /bin/bash
curl http://<service-name>.<namespace>.svc.cluster.local
```

**Problema 3: ImagePullBackOff**
```bash
# Verificar si ACR está attached
az aks check-acr --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME --acr $ACR_NAME

# Si no está, attacharlo
az aks update --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME --attach-acr $ACR_NAME
```

---

Esta es la guía completa de AKS. ¿Continúo con el siguiente servicio (**Azure Key Vault**)? 🔐