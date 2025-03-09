# k8s-stuck-pod-cleaner

A Kubernetes CronJob that automatically cleans up pods stuck in Terminating state.

![Architecture Diagram](docs/architecture.svg)

## Problem

In Kubernetes clusters, pods can sometimes get stuck in the Terminating state, especially when nodes become unhealthy. This can cause resource leaks, block deployments, and create critical situations in production environments.

## Solution

This project provides a simple yet effective solution: a Kubernetes CronJob that:
- Runs on a configurable schedule (default: every minute)
- Detects pods stuck in Terminating state
- Force deletes them after a configurable time threshold (default: 3 minutes)
- Creates Kubernetes events for audit and tracking

## Installation

### Option 1: Apply YAML directly

```bash
kubectl apply -f https://raw.githubusercontent.com/omarmfathy219/k8s-stuck-pod-cleaner/main/stuck-pod-cleaner.yaml
```

### Option 2: Install using Helm

```bash
# Add the repository
helm repo add omarmfathy219 https://omarmfathy219.github.io/helm-charts/

# Install the chart
helm install stuck-pod-cleaner omarmfathy219/stuck-pod-cleaner

# Or install with custom values
helm install stuck-pod-cleaner omarmfathy219/stuck-pod-cleaner --set podDeletion.minTimeBeforeDeletion=5
```

## Configuration

### YAML Configuration

The default configuration:
- Runs every minute
- Scans all namespaces
- Force deletes pods that have been in Terminating state for more than 3 minutes

You can customize these settings by editing the YAML file before applying it.

### Helm Configuration

When using Helm, you can customize the installation using values:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `namespace` | Namespace where the cleaner is deployed | `kube-system` |
| `namespaces.scanAll` | Whether to scan all namespaces | `true` |
| `namespaces.include` | List of namespaces to scan (if scanAll is false) | `[]` |
| `namespaces.exclude` | List of namespaces to exclude from scanning | `[]` |
| `cronJob.schedule` | CronJob schedule | `* * * * *` (every minute) |
| `podDeletion.minTimeBeforeDeletion` | Time threshold in minutes before force deletion | `3` |
| `podDeletion.createEvents` | Whether to create Kubernetes events | `true` |
| `resources.limits/requests` | Pod resource limits and requests | See values.yaml |

For a full list of configurable options, see the `values.yaml` file.

## How It Works

1. The CronJob runs on schedule and lists all pods in the cluster
2. It filters for pods in the Terminating state
3. For each terminating pod, it calculates how long it's been in that state
4. If a pod has been terminating for longer than the threshold, it force deletes it
5. It creates a Kubernetes event for auditing purposes

## RBAC

The solution includes the necessary RBAC resources:
- ServiceAccount: `pod-cleaner`
- ClusterRole: `pod-cleaner-role`
- ClusterRoleBinding: `pod-cleaner-binding`

These provide the minimum permissions required to list pods, delete pods, and create events.

## Repository Structure

```
.
├── README.md
├── stuck-pod-cleaner.yaml     # Standalone YAML for direct application
├── charts/                    # Helm chart directory
│   └── stuck-pod-cleaner/
│       ├── Chart.yaml         # Chart metadata
│       ├── values.yaml        # Default configuration values
│       └── templates/         # Chart templates
│           ├── cronjob.yaml   # Main CronJob definition
│           ├── rbac.yaml      # RBAC resources
│           └── NOTES.txt      # Installation notes
├── examples/
│   └── custom-configuration.yaml
├── CONTRIBUTING.md
└── LICENSE
```

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.