# kube-state

Cluster state definitions for the ShopHub platform. Each directory under `clusters/` describes a target cluster and its Helm releases.

## Structure

```
clusters/
└── local/                    # Minikube local development cluster
    ├── cluster.yaml          # Cluster metadata
    ├── shop-operator/        # CRD operator release
    ├── shophub/              # ShopHub admin service release
    └── shophub-discord/      # ShopHub with Discord channel config
```

Each release directory contains:
- `helm.yaml` — chart reference, version, namespace, and source
- `values.yaml` — environment-specific value overrides

## Local deployment

### Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/) running (`minikube start`)
- [Helm](https://helm.sh/) 3.14+
- Ingress addon enabled: `minikube addons enable ingress`

### Install shop-operator

```bash
helm dependency build charts/shop-operator
helm upgrade --install shop-operator charts/shop-operator \
  --namespace shop-operator \
  --create-namespace \
  -f clusters/local/shop-operator/values.yaml
```

### Install shophub

```bash
helm dependency build charts/shophub
helm upgrade --install shophub charts/shophub \
  --namespace shophub \
  --create-namespace \
  --set config.jwtSecret=<your-secret> \
  --set config.db.password=<your-password> \
  -f clusters/local/shophub/values.yaml
```

### Install shophub-discord

```bash
helm upgrade --install shophub-discord charts/shophub \
  --namespace shophub \
  --set config.jwtSecret=<your-secret> \
  --set config.db.password=<your-password> \
  -f clusters/local/shophub-discord/values.yaml
```

### Access

Add to `/etc/hosts`:
```
$(minikube ip)  shophub.local shophub-discord.local
```

Then open `http://shophub.local` in your browser.
