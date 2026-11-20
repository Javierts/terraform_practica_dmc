# Terraform – Azure Container Apps (práctica DMC)

Infraestructura como código para desplegar en Azure una aplicación Java en contenedor sobre **Azure Container Apps**, con acceso a secretos mediante **Key Vault** y una identidad administrada.

## Recursos que crea

| Archivo | Recurso | Descripción |
|---|---|---|
| `resource_group.tf` | Resource Group | Contenedor lógico de todos los recursos (`westus2`). |
| `container_app_env.tf` | Container App Environment + Container App | Entorno (perfil *Consumption*) y la app `mi-app-java` (imagen de Docker Hub, puerto 8080, ingress externo HTTPS, mínimo 3 réplicas). |
| `identity_user_assigned.tf` | User Assigned Identity + roles RBAC | Identidad de la app con rol *Key Vault Secrets User*; además asigna *Key Vault Administrator* a quien ejecuta Terraform. |
| `keyvault.tf` | Key Vault | Almacén de secretos con autorización RBAC. |
| `storage_account.tf` | Storage Account | Cuenta Standard LRS. |
| `azure_files.tf` | File Share | Recurso compartido de Azure Files (1 GB). |

La app recibe por variables de entorno `AZURE_CLIENT_ID` (identidad) y `KEY_VAULT_URI`, para leer secretos del Key Vault sin credenciales en el código.

## Requisitos

- [Terraform](https://developer.hashicorp.com/terraform/install) y [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli)
- Provider `hashicorp/azurerm` `= 4.4.0`
- Una suscripción de Azure con los namespaces `Microsoft.App`, `Microsoft.KeyVault`, `Microsoft.Storage`, `Microsoft.ManagedIdentity` y `Microsoft.OperationalInsights` registrados:
  ```bash
  az provider register --namespace Microsoft.App
  ```

## Uso

```bash
az login --tenant <tenant-id>
az account set --subscription <subscription-id>

terraform init
terraform plan
terraform apply
```

Configura el `subscription_id` en `provider.tf` y los nombres en `terraform.tfvars`.

> Los nombres del **Key Vault** y la **Storage Account** son únicos a nivel global en Azure; si ya están en uso, cámbialos en `terraform.tfvars`.

## Notas

- El state (`*.tfstate`), `.terraform/` y las claves SSH están en `.gitignore` y no se versionan.
- Para eliminar todo: `terraform destroy`.
