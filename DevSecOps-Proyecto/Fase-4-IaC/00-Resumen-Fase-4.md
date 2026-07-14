# Fase 4 — Infraestructura como Código (IaC)

## Objetivo
Definir y gestionar la infraestructura cloud mediante código versionado y automatizado.

## ¿Qué es IaC?

En vez de configurar servidores manualmente (clickops), defines tu infraestructura en archivos de código que se ejecutan automáticamente.

```
TRADICIONAL (ClickOps):
Servidor manual → Config manual → Deploy manual → "¿En qué versión está?"
                                                      ❌ No reproducible

IaC (Terraform):
terraform apply → Servidor creado → Config automática → Deploy automático
                                                      ✅ Reproducible y versionado
```

## Terraform

### Conceptos básicos

| Concepto | Definición | Ejemplo |
|----------|-----------|---------|
| **Provider** | Proveedor cloud | Azure, AWS, GCP |
| **Resource** | Componente de infra | VM, SQL Server, ACR |
| **State** | Estado actual de la infra | Terraform sabe qué ya creó |
| **Plan** | Vista previa de cambios | `terraform plan` |
| **Apply** | Ejecutar cambios | `terraform apply` |

### Estructura de archivos
```
infra/
├── main.tf           # Resources principales
├── variables.tf      # Variables de entrada
├── outputs.tf        # Valores de salida
├── providers.tf      # Configuración del provider
├── backend.tf        # State remoto
└── environments/
    ├── dev.tfvars    # Variables para desarrollo
    └── prod.tfvars   # Variables para producción
```

### Ejemplo básico (Azure)

```hcl
# providers.tf
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
}

# variables.tf
variable "resource_group_name" {
  description = "Nombre del Resource Group"
  type        = string
}

variable "location" {
  description = "Ubicación de los recursos"
  type        = string
  default     = "East US"
}

# main.tf
resource "azurerm_resource_group" "main" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_sql_server" "main" {
  name                         = "sqlserver${random_string.suffix.result}"
  resource_group_name          = azurerm_resource_group.main.name
  location                     = azurerm_resource_group.main.location
  version                      = "12.0"
  administrator_login          = var.sql_admin_login
  administrator_login_password = var.sql_admin_password  # ⚠️ Usar Key Vault
}

# outputs.tf
output "resource_group_id" {
  value = azurerm_resource_group.main.id
}
```

### Comandos Terraform
```bash
# Inicializar (descargar provider)
terraform init

# Ver plan de cambios
terraform plan -var-file="dev.tfvars"

# Aplicar cambios
terraform apply -var-file="dev.tfvars"

# Ver estado actual
terraform state list

# Destruir todo
terraform destroy -var-file="dev.tfvars"
```

## IaC Scanning (tfsec / checkov)

### tfsec
```bash
# Instalar
# Windows: choco install tfsec
# Mac: brew install tfsec

# Escanear
tfsec .

# Escanear con severidad mínima
tfsec . --minimum-severity HIGH
```

### checkov
```bash
# Instalar
pip install checkov

# Escanear directorio Terraform
checkov -d .

# Escanear solo archivos específicos
checkov -f main.tf
```

## State Management

### ¿Por qué importa?
El state file contiene información sensible (IDs, IPs, secrets). **NUNCA** commitear el state file al repo.

### Remote State (Azure Blob Storage)
```hcl
# backend.tf
terraform {
  backend "azurerm" {
    resource_group_name  = "tfstate-rg"
    storage_account_name = "tfstatestorage"
    container_name       = "tfstate"
    key                  = "dev.tfstate"
  }
}
```

### .gitignore para Terraform
```gitignore
*.tfstate
*.tfstate.*
.terraform/
*.tfvars  # Si contiene secrets
```

## Checklist IaC
- [ ] Terraform instalado y configurado
- [ ] Provider configurado (Azure/AWS/GCP)
- [ ] Resources definidos en código
- [ ] Variables parametrizadas por ambiente
- [ ] Remote state configurado
- [ ] tfsec/checkov sin findings Critical
- [ ] .gitignore excluye state files

## Notas personales
- 

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[DevSecOps-Proyecto/Referencia/Glosario-DevSecOps]]
