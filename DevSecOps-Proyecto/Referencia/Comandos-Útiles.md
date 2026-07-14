# Comandos Útiles — DevSecOps

## .NET

```bash
# Build
dotnet build
dotnet build -c Release

# Run
dotnet run --project TuProyecto.API

# Test
dotnet test
dotnet test --filter "FullyQualifiedName~TeamService"
dotnet test /p:CollectCoverage=true

# Format
dotnet format
dotnet format --verify-no-changes

# User Secrets
dotnet user-secrets init --project TuProyecto.API
dotnet user-secrets set "Key" "Value" --project TuProyecto.API
dotnet user-secrets list --project TuProyecto.API

# Vulnerable packages
dotnet list package --vulnerable
dotnet list package --vulnerable --include-transitive

# EF Core Migrations
dotnet ef migrations add MigrationName
dotnet ef database update
```

## Docker

```bash
# Build
docker build -t name:tag .

# Run
docker run -p 5000:8080 name:tag

# Compose
docker-compose up -d
docker-compose down
docker-compose logs -f

# Debugging
docker ps
docker exec -it container-id /bin/bash
docker logs container-id

# Cleanup
docker system prune -a
docker volume prune
```

## GitHub Actions

```bash
# Trigger workflow manually
gh workflow run "Workflow Name"

# View runs
gh run list

# View logs
gh run view <run-id>

# Cancel run
gh run cancel <run-id>
```

## Trivy (Security Scan)

```bash
# Scan image
trivy image name:tag

# Scan with severity filter
trivy image --severity CRITICAL,HIGH name:tag

# Scan Dockerfile
trivy config Dockerfile

# Scan filesystem
trivy fs .

# Output to JSON
trivy image -f json -o results.json name:tag
```

## Terraform

```bash
# Init
terraform init

# Plan
terraform plan
terraform plan -var-file="dev.tfvars"

# Apply
terraform apply
terraform apply -var-file="dev.tfvars"

# Destroy
terraform destroy

# State
terraform state list
terraform state show resource.name
```

## Kubernetes

```bash
# Pods
kubectl get pods
kubectl get pods -o wide
kubectl describe pod pod-name

# Logs
kubectl logs -f pod-name
kubectl logs pod-name -c container-name

# Exec
kubectl exec -it pod-name -- /bin/bash

# Deployments
kubectl get deployments
kubectl rollout status deployment/name

# Secrets
kubectl create secret generic name --from-literal=key=value
kubectl get secrets

# Network Policies
kubectl get networkpolicies

# Events
kubectl get events
kubectl get events --field-selector reason=Failed
```

## Git Security

```bash
# Gitleaks (scan for secrets)
gitleaks detect
gitleaks protect --staged

# Git history scan
gitleaks detect --log-opts="--all"

# Pre-commit hook
pre-commit install
pre-commit run --all-files
```

## Terraform Security (tfsec)

```bash
# Scan
tfsec .

# With severity filter
tfsec . --minimum-severity HIGH

# Format results
tfsec . --format json
```

## checkov (IaC scanning)

```bash
# Scan directory
checkov -d .

# Scan specific file
checkov -f main.tf

# Scan with specific check
checkov -d . --check CKV_AZURE_1
```

## Quick Reference

| Comando | Para qué |
|---------|----------|
| `dotnet test /p:CollectCoverage=true` | Tests con coverage |
| `trivy image name:tag` | Scan de imagen Docker |
| `gitleaks detect` | Scan de secretos |
| `tfsec .` | Scan de Terraform |
| `kubectl get pods` | Ver pods en K8s |
| `docker-compose up -d` | Levantar servicios |

## Links Relacionados
- [[00-Resumen-Fase-0]]
- [[Glosario-DevSecOps]]
