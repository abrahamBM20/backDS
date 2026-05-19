# Guía de Despliegue en AWS

## 📋 Pre-requisitos

### Secretos de GitHub requeridos (Settings > Secrets > Actions):

```
AWS_ACCESS_KEY_ID          - Access Key de AWS
AWS_SECRET_ACCESS_KEY      - Secret Key de AWS
AWS_SESSION_TOKEN          - Token de sesión (AWS Academy)
AWS_ACCOUNT_ID             - ID de tu cuenta AWS (12 dígitos)
EC2_INSTANCE_ID            - ID de tu instancia EC2 (i-xxxxxxxx)
```

### En tu EC2 necesitas:

- Docker instalado
- AWS CLI configurado
- Agent SSM running
- `docker-compose` instalado
- Archivo `/app/docker-compose.yml` en la instancia

---

## 🚀 Flujo de Despliegue

### 1. **Configurar repositorios ECR en AWS**

```bash
aws ecr create-repository --repository-name backend-ventas --region us-east-1
aws ecr create-repository --repository-name backend-despachos --region us-east-1
```

### 2. **Configurar variables de entorno en EC2**

En la instancia EC2, crea `/app/.env`:

```bash
MYSQL_ROOT_PASSWORD=tu_password_segura
MYSQL_PASSWORD=tu_password_bd
DB_PASSWORD=tu_password_bd
SPRING_PROFILES_ACTIVE=docker
```

### 3. **Preparar docker-compose.yml en EC2**

Sube el archivo `docker-compose.yml` del repositorio a `/app/docker-compose.yml`

### 4. **Hacer Push a rama 'deploy'**

```bash
git checkout deploy
git push origin deploy
```

El workflow de GitHub Actions se ejecutará automáticamente:
- ✅ Build de las imágenes Docker
- ✅ Push a ECR
- ✅ Despliegue en EC2 mediante SSM
- ✅ Validación de salud (health checks)

---

## 🔍 Monitoreo

### Ver logs del workflow
```bash
# En GitHub Actions → Deploy Capa Backend a AWS ECR y EC2
```

### Ver logs en EC2
```bash
docker logs backend-ventas
docker logs backend-despachos
docker logs mysql-backend
```

### Verificar servicios
```bash
curl http://EC2_IP:8080/actuator/health
curl http://EC2_IP:8081/actuator/health
```

---

## ⚠️ Cambios Realizados

### ✅ Mejorado:

1. **docker-compose.yml**
   - Variables de entorno desde `.env`
   - Health checks para MySQL
   - Restart policies
   - Logging configurado
   - Healthchecks en servicios

2. **docker-hub.yml (Workflow)**
   - ❌ Rutas corregidas (api-ventas y api-despachos)
   - ✅ Versionamiento de imágenes con SHA y timestamp
   - ✅ Docker Buildx con caché
   - ✅ Validación de imágenes ECR
   - ✅ Health checks post-deploy
   - ✅ Manejo de errores mejorado
   - ✅ SIN credenciales hardcodeadas

3. **Seguridad:**
   - ❌ Credenciales removidas de archivos
   - ✅ Todas en GitHub Secrets
   - ✅ Usuario no-root en Dockerfiles
   - ✅ Healthchecks activos

---

## 🛠️ Solución de Problemas

### Error: "Unknown repository"
```bash
# Crear repositorios ECR
aws ecr create-repository --repository-name backend-ventas
```

### Error: "Access Denied"
- Verifica AWS_ACCESS_KEY_ID y AWS_SECRET_ACCESS_KEY en Secrets
- Verifica permisos IAM de la cuenta AWS Academy

### Containers no inician
```bash
# En EC2:
docker-compose logs
docker ps -a
```

### MySQL connection refused
- Verifica health check de MySQL: `docker logs mysql-backend`
- Asegúrate que DB_ENDPOINT es correcto en .env

---

## 📝 Notas Importantes

- **Rama deploy**: Los cambios en esta rama disparan automáticamente el deploy
- **Variables sensibles**: NUNCA pushear `.env` al repositorio, usar `.env.example`
- **AWS Academy tokens**: Expiran después de 4 horas, actualizar Secrets regularmente
- **Imágenes**: Se versiona con SHA del commit + timestamp para trazabilidad
