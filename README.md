# Helm Chart - Sistema de Comentarios

Chart de Helm que despliega toda la solución en OpenShift.

## Componentes

- Frontend (Deployment + Service + Route)
- Backend-API (Deployment + Service + Route + HPA)
- Backend-Data (Deployment + Service)
- Database (StatefulSet + Service + PVC)
- NetworkPolicies (4 políticas de red)

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

## Despliegue Manual

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

## Verificar NetworkPolicies

```bash
oc get networkpolicies -n daniel-lopez-fisa-dev
oc describe networkpolicy frontend-netpol -n daniel-lopez-fisa-dev
```
