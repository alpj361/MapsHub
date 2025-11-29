# 🔧 Solución: Repositorio con Estructura Incorrecta

## 🔍 Problema Identificado

El proyecto se subió a GitHub con una estructura incorrecta. Render busca en:
```
/opt/render/project/src/backend-php
```

Pero el repositorio está estructurado de forma diferente.

---

## 📋 Primero: Verificar la Estructura Actual

Necesitamos ver cómo está el repo en GitHub. Ve a:
```
https://github.com/alpj361/MapsHub
```

**¿Qué ves en la raíz del repositorio?**

### Opción A: Archivos PHP directamente en la raíz
```
MapsHub/
├── index.php
├── router.php
├── Dockerfile
├── api/
├── config/
└── ...
```

### Opción B: Todo dentro de `backend-php/`
```
MapsHub/
└── backend-php/
    ├── index.php
    ├── Dockerfile
    └── ...
```

### Opción C: Estructura anidada incorrecta
```
MapsHub/
└── Desktop/
    └── backend-php/
        ├── index.php
        └── ...
```

---

## ✅ Soluciones según el caso

### SOLUCIÓN 1: Si los archivos están directamente en la raíz (Opción A)

**Acción:** Eliminar el `rootDir` del `render.yaml`

```yaml
services:
  - type: web
    name: collaborative-map-backend
    env: docker
    # rootDir: backend-php  ← ELIMINAR ESTA LÍNEA
    dockerfilePath: ./Dockerfile
    dockerContext: ./
    envVars:
      # ... resto de configuración
```

Luego:
```bash
git add render.yaml
git commit -m "fix: remove rootDir - files are in repo root"
git push origin main
```

---

### SOLUCIÓN 2: Si todo está en `backend-php/` (Opción B)

**Acción:** El `render.yaml` actual debería funcionar, pero asegúrate de que esté en la RAÍZ del repo, no dentro de `backend-php/`

**Estructura correcta:**
```
MapsHub/
├── render.yaml              ← DEBE estar aquí
└── backend-php/
    ├── Dockerfile
    ├── index.php
    └── ...
```

**Si `render.yaml` está dentro de `backend-php/`, muévelo:**

```bash
# Desde tu Mac (Desktop)
cd ~/Desktop
git clone https://github.com/alpj361/MapsHub.git
cd MapsHub

# Verificar estructura
ls -la

# Si render.yaml está en backend-php/, moverlo
mv backend-php/render.yaml ./render.yaml

# Commit y push
git add render.yaml
git rm backend-php/render.yaml
git commit -m "fix: move render.yaml to repo root"
git push origin main
```

---

### SOLUCIÓN 3: Si está anidado incorrectamente (Opción C) - Desktop/backend-php

**Acción:** Reorganizar el repositorio completamente

#### Opción 3A: Reorganizar localmente y hacer force push

```bash
# Desde tu Mac
cd ~/Desktop
git clone https://github.com/alpj361/MapsHub.git MapsHub-temp
cd MapsHub-temp

# Mover archivos al lugar correcto
mv Desktop/backend-php/* ./
rm -rf Desktop/

# O si prefieres mantener la estructura con backend-php:
mkdir backend-php-new
mv Desktop/backend-php/* backend-php-new/
rm -rf Desktop/
mv backend-php-new backend-php

# Commit y push
git add -A
git commit -m "fix: reorganize repository structure"
git push origin main
```

#### Opción 3B: Crear repo nuevo (más limpio)

```bash
# Desde tu Mac
cd ~/Desktop/backend-php

# Inicializar repo limpio
rm -rf .git  # CUIDADO: esto elimina el historial de git local
git init
git remote add origin https://github.com/alpj361/MapsHub.git

# Agregar archivos
git add .
git commit -m "Initial commit - clean structure"

# Force push (CUIDADO: esto sobrescribe el repo en GitHub)
git push -f origin main
```

---

## 🎯 Solución Rápida Recomendada

**Para verificar rápidamente qué estructura tienes:**

```bash
# En tu Mac
cd ~/Desktop
git clone https://github.com/alpj361/MapsHub.git
cd MapsHub
ls -laR | head -50
```

**Esto te mostrará la estructura. Luego aplica la solución correspondiente.**

---

## 📝 Configuraciones de render.yaml según estructura

### Si los archivos están en la raíz del repo:

```yaml
services:
  - type: web
    name: collaborative-map-backend
    env: docker
    dockerfilePath: ./Dockerfile
    dockerContext: ./
    # NO incluir rootDir
```

### Si los archivos están en backend-php/:

```yaml
services:
  - type: web
    name: collaborative-map-backend
    env: docker
    rootDir: backend-php
    dockerfilePath: ./Dockerfile
    dockerContext: ./
```

### Si la estructura es Desktop/backend-php/ (INCORRECTO - necesita reorganización):

```yaml
services:
  - type: web
    name: collaborative-map-backend
    env: docker
    rootDir: Desktop/backend-php
    dockerfilePath: ./Dockerfile
    dockerContext: ./
```

**⚠️ PERO NO RECOMIENDO ESTO. Mejor reorganiza el repo.**

---

## 🔍 Para Diagnosticar el Problema

Ejecuta estos comandos y comparte el resultado:

```bash
# Ver qué hay en el repo de GitHub
cd ~/Desktop
git clone https://github.com/alpj361/MapsHub.git MapsHub-check
cd MapsHub-check
echo "=== Estructura del repositorio ==="
find . -type f -name "*.php" -o -name "Dockerfile" -o -name "render.yaml" | head -20
echo "=== Contenido de la raíz ==="
ls -la
```

---

## ✅ Checklist Final

- [ ] Verificar estructura real del repo en GitHub
- [ ] Decidir qué solución aplicar (1, 2 o 3)
- [ ] Actualizar/mover `render.yaml` según corresponda
- [ ] Commit y push cambios
- [ ] Re-deploy en Render (manual o automático)
- [ ] Verificar logs en Render
- [ ] Probar endpoint: `/api/health`

---

**¿Qué opción describe mejor tu repositorio actual?** 
(A, B o C)

Una vez que lo confirmes, te doy los comandos exactos para arreglarlo.

