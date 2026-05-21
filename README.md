# k8s-n8n-pocharlies

n8n — workflow automation. DB: shared PostgreSQL (`postgres-shared`).

SQLite is not used for production. The `n8n-data` PVC is only for n8n local files and runtime data; workflow state lives in PostgreSQL.

## Cluster
- **Master**: x86 ubuntu (192.168.50.142), k3s v1.32.5
- **Workers**: nvidia-dgx (dgx1 ARM64), gx10-ec3d (dgx2 ARM64), sauvage (WireGuard edge)

## GitOps
Gestionado por ArgoCD desde [k8s-gitops-pocharlies](https://github.com/pocharlies/k8s-gitops-pocharlies).

## Environments

- Production keeps the legacy `k8s/manifest.yaml` path for the current ArgoCD app.
- `k8s/base` contains the shared production-equivalent manifest set.
- `k8s/overlays/prod` renders the production-equivalent base and is ready for a later no-op ArgoCD path migration.
- `k8s/overlays/stg` renders the staging instance:
  - namespace: `n8n-stg`
  - host: `n8n-stg.e-dani.com`
  - database: `n8n_stg`
  - database owner: `n8n_stg`
  - secret names: `n8n-stg-app`, `n8n-stg-postgres`, `n8n-stg-db-credentials`, `n8n-workflow-backup`

Staging is bootstrapped with Kubernetes Secrets because the current Vault role used by External Secrets cannot write new KV v2 metadata. Move the same values to these Vault paths before enabling the ExternalSecret manifests:

- `n8n/stg/app`
- `postgres-shared/users/n8n-stg`
- `n8n/stg/workflow-backup`
