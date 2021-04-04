# Flux CD Demo

We will be deploying [podinfo](https://github.com/stefanprodan/podinfo) with Flux CD. This repository supports [Helm](https://helm.sh/) for deploys. Flux CD will make use of the [Helm Controller](https://github.com/fluxcd/kustomize-controller).

## Requirements
  - [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
  - [k3d](https://k3d.io/)
  - [flux](https://github.com/fluxcd/flux2#flux-installation
  - [Gitlab account](https://gitlab.com/)

## Create local cluster
```sh
# Create a Kubernetes cluster using k3d and configure kubectl
k3d cluster create -a 2 gitops
k3d kubeconfig merge gitops --switch-context
```

## Configure Flux CD
```sh
# Export Gitlab configuration
export GITLAB_GROUP=<your-group>
export GITLAB_REPOSITORY=<your-repository>
export GITLAB_TOKEN=<your-token>
export GITLAB_USER=<your-user>

# Run the bootstrap command:
flux bootstrap gitlab \
  --owner=$GITLAB_USER \
  --repository=$GITLAB_REPOSITORY \
  --owner=$GITLAB_GROUP \
  --branch=feat/demo \
  --path=staging-cluster \
  --personal

# Check Flux progress
watch flux get helmreleases -n default

# Check kustomize controler logs
kubectl -n flux-system logs -f deployment/helm-controller

# Check resource creation
watch kubectl get pods

# Port-forward to access Prometheus
kubectl port-forward service/prometheus-server 8000:80

# Check webapp
http://localhost:8000/
```
