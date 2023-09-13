# Helm Chart to Operator SDK Containerization

This guide will walk you through the process of creating a Helm chart and then using Operator SDK to containerize the Helm chart for managing Kubernetes applications.

## Prerequisites

Before you begin, make sure you have the following prerequisites installed:

- Kubernetes Cluster
- Helm ([Installation Guide](https://helm.sh/docs/intro/install/))
- Operator SDK ([Installation Guide](https://sdk.operatorframework.io/docs/install-operator-sdk/))

## Step 1: Create a Helm Chart

1. Create a new directory for your Helm chart:

```
mkdir my-helm-chart && cd my-helm-chart
```

## Step 2 Initialize A Helm chart structure:

 1. Generate Helm files
```
helm create my-chart
```

 2. Modify the values.yaml file in the my-chart/ directory to define default values and configuration options for your Helm chart.
```
cd  my-chart/
```

## Step 3 Confirm templates output with values:

 1. Run dry-run command to output values to screen without deploying to kubernetes
```
helm install --dry-run --debug realese-name ./my-chart
```

## Step 4 Deploy application with helm:

 1. Deploy application on Kubernetes (confirm you are in namespace you want to deploy application in)
```
helm install release-name ../my-chart
```

 2. Undeploy app on Kubernetes
```
helm uninstall release-name
```


## Create an Operator Project

 1. Create a new directory for your Operator project:
```
cd .. && mkdir my-operator && cd my-operator
```

 2. Initialize a new Operator project:
```
operator-sdk init --plugins=helm \
      --domain=example.com \
      --helm-chart=../my-chart/
```

 3. Modify file to specify nameapce
```
vi config/default/kustomization.yaml
```

 4. Modify memory limit under spec.container.resources.limits
   **app will crash if more memory is needed - default max memory is 128Mi**
```
vi config/d efault/manager_auth_proxy_patch.yaml
```

 5. Modify memory limits under spec.containers.args.resources.limits
 ```
 vi config/manager/manager.yaml
 ```
 
 6. Containerize Helm and push operator image to registry
```
make docker-build docker-push IMG=path/to/operator/image:repo
```

 7. watch pods for any errors in seprate terminal
```
kubectl get pods --watch
```

 8. Deploy operator to kubernetes
```
make deploy IMG=path/to/operator/image:repo
```

 9. Use the Operator to deploy Helm Chart
```
kubectl apply -f  ./my-helm-chart/my-operator/config/samples/charts_v1alpha1_helm.yaml 
```

 10. Undeploy app
 ```
 make undeploy
 ```
