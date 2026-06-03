# kube-state

Cluster state definitions for the ShopHub platform. Each directory under `clusters/` describes a target cluster and its Helm releases.

## Structure

```
clusters/
└── local/                    # Docker Desktop local development cluster
    ├── cluster.yaml          # Cluster metadata
    ├── alerts/               # PrometheusRule alert definitions
    ├── grafana/              # Grafana dashboard ConfigMaps
    ├── monitoring/           # Observability stack values (Prometheus, Grafana, Loki, Tempo)
    ├── shop-operator/        # Shop operator Helm release values
    └── shophub/              # ShopHub Helm release values
```

Each Helm release directory contains:
- `helm.yaml` — chart reference, version, namespace, and source
- `values.yaml` — environment-specific value overrides

## Local deployment

### Prerequisites

- Docker Desktop with Kubernetes enabled (Settings → Kubernetes → Enable Kubernetes)
- PowerShell 5.1+
- [Helm](https://helm.sh/) 3.14+ in PATH
- `kubectl` in PATH

### Pokretanje (first time)

```powershell
# From the repo root (parent of kube-state/) — run as Administrator
.\pokretanje.ps1
```

The script installs the full stack. When running for the first time it will
interactively ask for two secrets that are never stored in the repo:

- **Discord bot token** — used by the shop-operator to auto-create a
  notification channel for every shop. Get it from a team member or the
  Discord Developer Portal.
- **Platform webhook URL** — Alertmanager uses this to send cluster-level
  and ShopHub alerts to Discord. Get it from the Discord channel settings
  (Channel Settings → Integrations → Webhooks) or ask a team member.

Both prompts offer Enter-to-skip. If skipped, Discord integration won't work
but the rest of the stack runs normally. You can set the secrets later and run
`pokretanje.ps1` again — existing values are never overwritten.

Per-shop Discord channels and webhooks are created automatically by the
operator after each shop is provisioned — no further setup needed.

### Grafana

```powershell
kubectl port-forward -n monitoring svc/kube-prometheus-stack-grafana 3000:80
# Open http://localhost:3000  (admin / shophub-maintainer-2026)
```

Or use `.\apply-observability.ps1 -OpenGrafana` from the repo root.

### Access

ShopHub is available at `http://shophub.local` (added automatically to
`hosts` by `pokretanje.ps1` when run as Administrator).
