# Helm Chart - Sistema de Comentarios

Chart de Helm que despliega toda la solución en OpenShift.

## Componentes

- Frontend (Deployment + Service + Route)
- Backend-API (Deployment + Service + Route + HPA)
- Backend-Data (Deployment + Service)
- Database (StatefulSet + Service + PVC)
- NetworkPolicies (4 políticas de red)

## Secrets Requeridos en GitHub

Este repositorio necesita 4 secrets configurados:

```
DOCKER_USERNAME    → Usuario de Docker Hub
DOCKER_TOKEN       → Token de Docker Hub
OPENSHIFT_SERVER   → URL del servidor OpenShift
OPENSHIFT_TOKEN    → Token de autenticación OpenShift
```

Ver [../SECRETS.md](../SECRETS.md) para instrucciones detalladas.

## Workflow CI/CD

El pipeline hace:
1. **Package:** Empaqueta el Helm Chart y lo sube a Docker Hub (OCI)
2. **Deploy:** Se autentica en OpenShift y despliega con `helm upgrade --install`

## Actualizar Tags

Antes de hacer push, actualizar en `comments-chart/values.yaml`:

```yaml
frontend:
  tag: <BUILD_NUMBER_FRONTEND>
backendApi:
  tag: <BUILD_NUMBER_API>
backendData:
  tag: <BUILD_NUMBER_DATA>
database:
  tag: <BUILD_NUMBER_DB>
```

## Despliegue Automático

```bash
git add .
git commit -m "Deploy version X"
git push
```

GitHub Actions automáticamente:
- Empaqueta el chart
- Lo sube a Docker Hub
- Se conecta a OpenShift
- Despliega la aplicación

## Despliegue Manual (Opcional)

```bash
# Descargar chart
helm pull oci://registry-1.docker.io/<USERNAME>/comments-chart --version <VERSION>

# Instalar
helm install comments-system ./comments-chart-<VERSION>.tgz

# Actualizar
helm upgrade comments-system ./comments-chart-<VERSION>.tgz

# Desinstalar
helm uninstall comments-system
```

## Verificar Despliegue

```bash
# Ver pods
oc get pods -n daniel-lopez-fisa-dev

# Ver routes
oc get routes -n daniel-lopez-fisa-dev

# Ver networkpolicies
oc get networkpolicies -n daniel-lopez-fisa-dev

# Logs del workflow
# Ver en GitHub Actions del repositorio
```

## Troubleshooting

### Error de autenticación en OpenShift
- Verificar que `OPENSHIFT_TOKEN` sea válido
- Renovar token: `oc whoami -t`

### Error de permisos
- Verificar que el usuario tenga permisos en el namespace
- Verificar que el namespace exista

### Pods no inician
- Verificar que las imágenes Docker existan
- Verificar tags en values.yaml
