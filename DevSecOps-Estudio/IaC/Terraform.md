# Terraform — IaC

## QUÉ es

Herramienta de **Infraestructura como Código** que permite definir, provisionar y gestionar infraestructura cloud con archivos de configuración.

## Conceptos

| Concepto | Definición | Ejemplo |
|----------|-----------|---------|
| **Provider** | Proveedor cloud | Azure (azurerm), AWS (aws) |
| **Resource** | Componente de infra | VM, SQL Server, Container Registry |
| **Variable** | Parámetro de entrada | `var.location` |
| **Output** | Valor de salida | IP de la VM |
| **State** | Estado actual de la infra | Qué ya existe |
| **Module** | Bloque reutilizable | Paquete de resources relacionados |
| **Backend** | Dónde guardar el state | Azure Blob, S3 |

## Flujo de trabajo

```
1. Escribe .tf files
         │
         ▼
2. terraform init     (descarga providers)
         │
         ▼
3. terraform plan     (vista previa)
         │
         ▼
4. terraform apply    (crea/modifica infra)
         │
         ▼
5. State guardado     (Terraform sabe qué creó)
```

## Estructura de archivos

```
infra/
├── main.tf           # Resources
├── variables.tf      # Variables
├── outputs.tf        # Outputs
├── providers.tf      # Providers
├── backend.tf        # State remoto
└── environments/
    ├── dev.tfvars
    └── prod.tfvars
```

## Ejemplo básico

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

provider "azurerm" { features {} }

# variables.tf
variable "location" {
  type    = string
  default = "East US"
}

# main.tf
resource "azurerm_resource_group" "main" {
  name     = "mi-rg"
  location = var.location
}

# outputs.tf
output "rg_id" {
  value = azurerm_resource_group.main.id
}
```

## Comandos esenciales

```bash
terraform init          # Inicializar
terraform plan          # Ver cambios
terraform apply         # Aplicar
terraform destroy       # Destruir
terraform state list    # Ver estado
terraform fmt           # Formatear código
```

## SEGURIDAD

| Regla | Por qué |
|-------|---------|
| **NUNCA** commitear `.tfstate` | Contiene secrets |
| Usar remote state | Backup y collaboration |
| tfsec/checkov | Detectar configs inseguras |
| Variables para secrets | No hardcodear passwords |
| State locking | Evitar conflictos |

## Referencia
- [[DevSecOps-Estudio/00-Index]]
- [[DevSecOps-Proyecto/Fase-4-IaC/00-Resumen-Fase-4]]
