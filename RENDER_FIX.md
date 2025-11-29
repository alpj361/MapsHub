# 🔧 Solución al Error de Render: Root Directory Missing

## ❌ Error Actual

```
==> Service Root Directory "/opt/render/project/src/backend-php" is missing.
error: invalid local: resolve : lstat /opt/render/project/src/backend-php: no such file or directory
```

## 🔍 Diagnóstico

Este error ocurre porque Render no puede encontrar el directorio del servicio. Hay dos posibles causas:

### Causa 1: `render.yaml` en ubicación incorrecta

El archivo `render.yaml` **DEBE estar en la raíz del repositorio** `MapsHub`, NO dentro de `backend-php`.

**Estructura CORRECTA del repositorio:**

```
MapsHub/                    ← Raíz del repo en GitHub
├── render.yaml             ← DEBE estar aquí (raíz)
├── backend-php/            ← Tu código está aquí
│   ├── Dockerfile
│   ├── index.php
│   ├── api/
│   └── ...
└── (otros directorios si los hay)
```

### Causa 2: Servicio creado manualmente con configuración incorrecta

Si creaste el servicio manualmente en Render Dashboard, la configuración del "Root Directory" podría estar incorrecta.

---

## ✅ Soluciones

### Solución 1: Mover `render.yaml` a la raíz del repositorio

1. **Verifica la estructura actual:**

```bash
# Desde la raíz del repo MapsHub (no desde backend-php)
cd /ruta/al/MapsHub
ls -la
```

2. **Si `render.yaml` está dentro de `backend-php/`, muévelo:**

```bash
# Desde la raíz del repo MapsHub
mv backend-php/render.yaml ./render.yaml
```

3. **Actualiza el `render.yaml` movido:**

El `render.yaml` en la raíz debe tener `rootDir: backend-php`:

```yaml
services:
  - type: web
    name: collaborative-map-backend
    env: docker
    rootDir: backend-php          # ← Apunta al subdirectorio
    dockerfilePath: ./Dockerfile   # ← Ruta relativa desde backend-php
    dockerContext: ./              # ← Contexto relativo desde backend-php
    envVars:
      # ... resto de configuración
```

**⚠️ IMPORTANTE:** Las rutas `dockerfilePath` y `dockerContext` son relativas al `rootDir`, así que deben ser `./Dockerfile` y `./` respectivamente.

4. **Commit y push:**

```bash
git add render.yaml
git commit -m "fix: move render.yaml to repo root for Render deployment"
git push origin main
```

### Solución 2: Si el servicio fue creado manualmente

Si creaste el servicio desde Render Dashboard (no usando Blueprint):

1. Ve a tu servicio en [Render Dashboard](https://dashboard.render.com)
2. Click en **Settings**
3. Busca **"Root Directory"**
4. Cambia a: `backend-php` (sin `src/` ni barras iniciales)
5. Click **Save Changes**
6. Haz un **Manual Deploy**

### Solución 3: Usar Blueprint (Recomendado)

1. **Asegúrate de que `render.yaml` esté en la raíz del repo**
2. En Render Dashboard:
   - Ve a **Blueprints**
   - Si ya existe uno, click **"Manual Sync"**
   - Si no existe, crea uno nuevo conectando tu repo
3. Render detectará automáticamente el `render.yaml` y creará/actualizará el servicio

---

## 🧪 Verificación

Después de aplicar la solución:

1. **Revisa los logs en Render:**
   - Deberías ver: `==> Cloning from https://github.com/alpj361/MapsHub`
   - Luego: `==> Building Docker image...`
   - NO deberías ver el error de "Root Directory missing"

2. **Verifica que el build continúe:**
   ```
   ==> Building Docker image...
   ==> Deploying...
   ```

3. **Prueba el endpoint:**
   ```
   https://tu-servicio.onrender.com/api/health
   ```

---

## 📝 Notas Importantes

### Sobre `rootDir` vs `rootDirectory`

- Render acepta ambos: `rootDir` y `rootDirectory`
- He cambiado a `rootDir` porque es más común en la documentación reciente
- Ambos deberían funcionar, pero si uno no funciona, prueba el otro

### Sobre las rutas en `render.yaml`

Cuando usas `rootDir: backend-php`:
- `dockerfilePath: ./Dockerfile` → Busca `backend-php/Dockerfile`
- `dockerContext: ./` → Usa `backend-php/` como contexto de build

### Estructura del repositorio

Render espera esta estructura cuando usas `rootDir`:

```
repo-root/
├── render.yaml          ← Configuración (raíz)
└── backend-php/         ← Código del servicio
    ├── Dockerfile       ← Referenciado como ./Dockerfile
    └── ...
```

---

## 🆘 Si el problema persiste

1. **Verifica la estructura del repo en GitHub:**
   - Ve a: `https://github.com/alpj361/MapsHub`
   - Confirma que `render.yaml` está en la raíz
   - Confirma que `backend-php/` existe y contiene el código

2. **Revisa los logs completos en Render:**
   - Ve a tu servicio → **Logs**
   - Busca el error completo
   - Comparte los logs si necesitas más ayuda

3. **Prueba localmente:**
   ```bash
   cd backend-php
   docker build -t test .
   docker run -p 8000:80 test
   ```

4. **Alternativa: Deploy sin `rootDir`:**
   Si nada funciona, puedes mover todo el contenido de `backend-php/` a la raíz del repo y eliminar `rootDir` del `render.yaml`. Pero esto requiere reorganizar el repo.

---

## ✅ Checklist Final

- [ ] `render.yaml` está en la raíz del repositorio `MapsHub`
- [ ] `render.yaml` tiene `rootDir: backend-php`
- [ ] El directorio `backend-php/` existe en el repo
- [ ] `Dockerfile` está en `backend-php/Dockerfile`
- [ ] Cambios están commiteados y pusheados a GitHub
- [ ] Blueprint sincronizado en Render (o servicio actualizado manualmente)
- [ ] Build exitoso en Render
- [ ] Endpoint `/api/health` responde correctamente

---

¡Con estos pasos deberías resolver el problema! 🚀

