# dynacat

[Dynacat](https://github.com/Panonim/dynacat) is a dashboard forked from Glance, with a web UI editor.

```sh
helm repo add dawidd6 https://dawidd6.github.io/charts
helm install dynacat dawidd6/dynacat --values values.yaml
```

## Configuration

`config` holds the contents of `dynacat.yml` ([reference](https://github.com/Panonim/dynacat/blob/main/docs/docs/configuration.md)).
`${ENV_VAR}` references in it are resolved by Dynacat from the container environment, which `env` and `envFrom` populate:

```yaml
config:
  auth:
    secret-key: ${DYNACAT_SECRET_KEY}
  pages:
    - name: Home
      columns:
        - size: full
          widgets:
            - type: clock
env:
  DYNACAT_SECRET_KEY:
    valueFrom:
      secretKeyRef:
        name: dynacat-secrets
        key: secret-key
```

The web UI editor saves to the config file, so the chart copies `config` from a ConfigMap into a writable directory when the pod starts:

- `persistence.enabled: false` (default): the directory is an `emptyDir`. Every pod starts from `config`, a changed `config` rolls the pod, and UI edits are lost on restart.
- `persistence.enabled: true`: the directory is a PersistentVolumeClaim, seeded from `config` only once. Afterwards the UI owns the config; delete `dynacat.yml` on the volume to re-seed it.

## Values

| Key | Default | Description |
| --- | ------- | ----------- |
| `image.repository` | `docker.io/panonim/dynacat` | Image |
| `image.tag` | see `values.yaml` | Image tag |
| `config` | a page with a clock | Contents of `dynacat.yml` |
| `persistence.enabled` | `false` | Keep UI edits in a PersistentVolumeClaim |
| `persistence.storageClass` | `""` | Storage class of the claim |
| `persistence.size` | `1Gi` | Size of the claim |
| `persistence.existingClaim` | `""` | Use an existing claim instead |
| `env` | `{}` | Environment variables: `NAME: value` or `NAME: {valueFrom: ...}` |
| `envFrom` | `[]` | Environment variable sources |
| `service.port` | `8080` | Service port |
| `httpRoute.enabled` | `false` | Create a Gateway API HTTPRoute (`parentRefs`, `hostnames`) |
| `ingress.enabled` | `false` | Create an Ingress (`className`, `hosts`, `tls`) |
| `podSecurityContext`, `securityContext` | non-root, read-only root filesystem | Pod and container security contexts |
| `resources`, `nodeSelector`, `tolerations`, `affinity` | empty | Usual scheduling settings |
