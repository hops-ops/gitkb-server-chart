# gitkb-server

Helm chart for running `git-kb serve` as a single-replica, PVC-backed Kubernetes service.

This chart intentionally does not configure authentication. Keep the Service internal-only until Gateway/OIDC or GitKB-native auth is added in a later slice.

## Quick Start

```sh
helm install gitkb ./xrs/charts/gitkb-server --namespace gitkb --create-namespace
```

Then port-forward the service:

```sh
kubectl -n gitkb port-forward svc/gitkb-gitkb-server 8080:8080
```

From another KB:

```sh
git-kb remote add served http://127.0.0.1:8080
git-kb pull served --type note
git-kb push served 'notes/my-note'
```

## Persistence

By default the chart creates a `10Gi` `ReadWriteOnce` PVC through the StatefulSet `volumeClaimTemplates`.

For test-only ephemeral mode:

```yaml
persistence:
  enabled: false
```

## Seeding

Empty KB by default:

```yaml
seed:
  mode: empty
```

Restore from a Secret-mounted backup:

```yaml
seed:
  mode: backup
  secretRef:
    name: gitkb-backup
    key: backup.json
```

Apply a bundle:

```yaml
seed:
  mode: bundle
  secretRef:
    name: gitkb-bundle
    key: delta.kbbundle
```
