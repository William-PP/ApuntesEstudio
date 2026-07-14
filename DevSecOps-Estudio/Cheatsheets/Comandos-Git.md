# Cheatsheet — Git

## Comandos esenciales

```bash
# ═══════════════════════════════════════════════════
# BÁSICOS
# ═══════════════════════════════════════════════════
git init                            # Inicializar repo
git clone url                       # Clonar repo
git add .                           # Stagear todo
git add file                        # Stagear archivo
git commit -m "mensaje"             # Commit
git push origin main                # Push
git pull origin main                # Pull
git status                          # Estado
git log --oneline                   # Historial compacto
git log --oneline -10               # Últimos 10 commits

# ═══════════════════════════════════════════════════
# RAMAS
# ═══════════════════════════════════════════════════
git branch                          # Ver ramas
git branch feature-name             # Crear rama
git checkout feature-name           # Cambiar a rama
git checkout -b feature-name        # Crear y cambiar
git merge feature-name              # Merge
git branch -d feature-name          # Eliminar rama

# ═══════════════════════════════════════════════════
# DESHACER
# ═══════════════════════════════════════════════════
git stash                           # Guardar cambios temporales
git stash pop                       # Recuperar stash
git reset HEAD file                 # Unstage
git reset --soft HEAD~1             # Deshacer último commit (mantener cambios)
git reset --hard HEAD~1             # Deshacer último commit (eliminar cambios)

# ═══════════════════════════════════════════════════
# SEGURIDAD
# ═══════════════════════════════════════════════════
git log --oneline --all             # Ver todo el historial
git log --diff-filter=D -- "*.env"  # Ver si se borró .env
gitleaks detect                     # Escanear secretos
gitleaks protect --staged           # Escanear staged
```

## Referencia
- [[00-Index]]
- [[CI-CD/Secret-Scanning]]
