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

The repo root contains `pokretanje.ps1` which installs the full stack.
**Before running it**, set up the two secrets below — they are not stored in
the repo (secrets never go in git).

#### Step 1 — Discord bot token (one-time per machine)

The shop-operator uses a Discord bot to auto-create a notification channel
for every shop. Get the token from the Discord Developer Portal (ask a team
member) and run:

```powershell
kubectl create namespace shop-operator
kubectl create secret generic shop-operator-discord-bot `
  -n shop-operator `
  --from-literal=bot-token='<BOT_TOKEN>'
```

#### Step 2 — Platform alertmanager webhook (one-time per machine)

Prometheus routes cluster-level and ShopHub alerts to a Discord channel via
this webhook. Get the URL from the Discord channel settings (ask a team member)
or create a new one with the bot. Then run:

```powershell
kubectl create namespace monitoring
kubectl create secret generic alertmanager-discord-secret `
  -n monitoring `
  --from-literal=webhook-url='<WEBHOOK_URL>'
```

#### Step 3 — Run the stack

```powershell
# From the repo root (parent of kube-state/)
.\pokretanje.ps1
```

`pokretanje.ps1` detects both secrets above and skips applying the
placeholder YAML from this repo, so your real values are never clobbered.
Per-shop Discord channels and webhooks are created automatically by the
operator — no further setup needed.

### Grafana

```powershell
kubectl port-forward -n monitoring svc/kube-prometheus-stack-grafana 3000:80
# Open http://localhost:3000  (admin / shophub-maintainer-2026)
```

Or use `.\apply-observability.ps1 -OpenGrafana` from the repo root.

### Access

ShopHub is available at `http://shophub.local` (added automatically to
`hosts` by `pokretanje.ps1` when run as Administrator).
