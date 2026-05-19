# ✅ Checklist Pre-Deploy a AWS

## Configuración Local

- [ ] `.env` creado con variables de entorno (NO pushear al repo)
- [ ] `.env` en `.gitignore`
- [ ] `docker-compose.yml` actualizado con variables de entorno
- [ ] Dockerfiles sin credenciales hardcodeadas
- [ ] Ambos Dockerfiles tienen healthchecks
- [ ] Build local funciona: `docker-compose up -d`
- [ ] Servicios responden:
  ```bash
  curl http://localhost:8080/actuator/health
  curl http://localhost:8081/actuator/health
  ```

## AWS Academy Setup

- [ ] AWS Academy Learner Lab accesible
- [ ] Access Key generada (temporal)
- [ ] Secret Key generada
- [ ] Session Token disponible
- [ ] Región: us-east-1

## GitHub Secrets Configurados

- [ ] `AWS_ACCESS_KEY_ID` ✓
- [ ] `AWS_SECRET_ACCESS_KEY` ✓
- [ ] `AWS_SESSION_TOKEN` ✓
- [ ] `AWS_ACCOUNT_ID` (12 dígitos) ✓
- [ ] `EC2_INSTANCE_ID` (i-xxxxxxxx) ✓

## Repositorios ECR Creados

```bash
aws ecr create-repository --repository-name backend-ventas --region us-east-1
aws ecr create-repository --repository-name backend-despachos --region us-east-1
```

- [ ] backend-ventas repo creado
- [ ] backend-despachos repo creado

## Instancia EC2 Configurada

- [ ] EC2 instancia corriendo
- [ ] Security Group permite puertos:
  - [ ] 22 (SSH)
  - [ ] 3306 (MySQL)
  - [ ] 8080 (Ventas API)
  - [ ] 8081 (Despachos API)
- [ ] Docker instalado
- [ ] Docker Compose instalado
- [ ] AWS CLI v2 instalado
- [ ] Systems Manager Agent corriendo
- [ ] `/app` directory creado en EC2
- [ ] `.env` copiado a `/app/.env` en EC2
- [ ] `docker-compose.yml` copiado a `/app/docker-compose.yml` en EC2

## Verificación de Archivos

- [ ] `docker-compose.yml` con variables de entorno ✓
- [ ] `github/workflows/docker-hub.yml` actualizado ✓
- [ ] Ambos `Dockerfile` sin credenciales ✓
- [ ] `.env.example` creado ✓

## Primer Deploy

1. Push a rama `deploy`:
   ```bash
   git checkout deploy
   git push origin deploy
   ```

2. Monitorear en GitHub Actions

3. Si exitoso, verificar en EC2:
   ```bash
   docker ps
   docker-compose logs
   curl http://EC2_IP:8080/actuator/health
   curl http://EC2_IP:8081/actuator/health
   ```

## Post-Deploy

- [ ] Ambas APIs responden
- [ ] MySQL tiene datos
- [ ] Logs son normales
- [ ] No hay credenciales en workflows o Dockerfiles
- [ ] Versionamiento de imágenes visible en ECR

---

## 🚨 Seguridad - VERIFICAR

- [ ] NO hay passwords en Dockerfiles
- [ ] NO hay passwords en docker-compose.yml
- [ ] NO hay passwords en .yml workflows
- [ ] `.env` está en `.gitignore`
- [ ] Todos los secretos están en GitHub Secrets
- [ ] Usuario no-root en Dockerfiles (springuser)
- [ ] Health checks activos en todos los servicios
