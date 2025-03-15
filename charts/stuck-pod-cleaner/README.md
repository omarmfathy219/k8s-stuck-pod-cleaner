# Stuck Pod Cleaner

A Kubernetes CronJob that automatically cleans up pods stuck in Terminating state.

## Introduction

In Kubernetes clusters, pods can sometimes get stuck in the Terminating state, especially when nodes become unhealthy. This can cause resource leaks, block deployments, and create critical situations in production environments.

The Stuck Pod Cleaner provides an automated solution that:
- Runs on a configurable schedule (default: every minute)
- Detects pods stuck in Terminating state
- Force deletes them after a configurable time threshold (default: 3 minutes)
- Creates Kubernetes events for audit and tracking

## Problem

When a node becomes unhealthy in a Kubernetes cluster, pods scheduled on that node can get stuck in the Terminating state. This happens because the kubelet on the unresponsive node cannot perform the normal pod termination process. In production environments, this can lead to:

- Blocked deployments when replacement pods can't be scheduled
- Resource leaks as the cluster continues to track these pods
- Challenges during cluster maintenance operations
- Manual intervention to clean up, which is error-prone and time-consuming

## Solution

This Helm chart deploys a CronJob that automatically:
1. Scans your cluster for pods stuck in Terminating state
2. Identifies pods that have been stuck for longer than a configurable threshold
3. Force deletes these pods to free up resources
4. Creates Kubernetes events for auditing and tracking

## Installation

```bash
# Add the repository
helm repo add omarmfathy219 https://omarmfathy219.github.io/helm-charts/

# Install the chart
helm install stuck-pod-cleaner omarmfathy219/stuck-pod-cleaner
```

To install with custom values:
```bash
helm install stuck-pod-cleaner omarmfathy219/stuck-pod-cleaner --set podDeletion.minTimeBeforeDeletion=5
```

## Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `namespace` | Namespace where the cleaner is deployed | `kube-system` |
| `namespaces.scanAll` | Whether to scan all namespaces | `true` |
| `namespaces.include` | List of namespaces to scan (if scanAll is false) | `[]` |
| `namespaces.exclude` | List of namespaces to exclude from scanning | `[]` |
| `cronJob.schedule` | CronJob schedule | `* * * * *` (every minute) |
| `cronJob.concurrencyPolicy` | How to handle concurrent executions | `Forbid` |
| `cronJob.successfulJobsHistoryLimit` | How many successful jobs to keep in history | `3` |
| `cronJob.failedJobsHistoryLimit` | How many failed jobs to keep in history | `3` |
| `cronJob.activeDeadlineSeconds` | Timeout for job execution | `180` |
| `podDeletion.minTimeBeforeDeletion` | Time threshold in minutes before force deletion | `3` |
| `podDeletion.createEvents` | Whether to create Kubernetes events | `true` |
| `resources.limits.cpu` | CPU limits for the container | `100m` |
| `resources.limits.memory` | Memory limits for the container | `128Mi` |
| `resources.requests.cpu` | CPU requests for the container | `50m` |
| `resources.requests.memory` | Memory requests for the container | `64Mi` |

## Security

This Helm chart is cryptographically signed for enhanced security. To verify the signature during installation:

```bash
helm install --verify stuck-pod-cleaner omarmfathy219/stuck-pod-cleaner
```

The solution follows security best practices:
- Uses a dedicated ServiceAccount with minimal permissions
- Runs with non-root user
- Has read-only filesystem access
- Includes resource limits to prevent excessive resource consumption

## Monitoring and Troubleshooting

To check if the cleaner is running properly:

```bash
# Check CronJob status
kubectl get cronjobs -n kube-system stuck-pod-cleaner

# View logs from recent job executions
kubectl logs -n kube-system -l app=stuck-pod-cleaner

# See events created by the cleaner
kubectl get events --all-namespaces --field-selector source=pod-cleaner-controller
```

## Source Code

The source code for this project is available on GitHub:
[https://github.com/omarmfathy219/k8s-stuck-pod-cleaner](https://github.com/omarmfathy219/k8s-stuck-pod-cleaner)

## License

This project is licensed under the MIT License.