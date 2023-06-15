# contour-package

This package provides contour functionality using [projectcontour.io](https://projectcontour.io/).

## Components

* Project contour

## Configuration

The following configuration values can be set to customize the project contour installation.

### Global

| Value | Required/Optional | Default     | Description |
|-------|-------------------|-------------|-------------|
| `namespace` | Required | **projectcontour** | The namespace in which to deploy contour |
| `create_namespace` | Required | **True** | Needs the namespace where contour will be installed to be created? |
| `privileged_clusterrole_name` | Optional | <EMPTY> | ClusterRole name to start envoy as privileged if PSPs are enabled |
| `is_externaldns_enabled` | Required | **False** | Is external_dns going to be available in the cluster? |
| `externaldns.domain` | Optional | <EMPTY> | The main DNS domain in which contour will be registered. Only required when external_dns is enabled |
| `externaldns.wildcard_domain` | Optional | <EMPTY> | The applications wildcard DNS domain in which contour will be registered. Only required when external_dns is enabled |

## Usage Example

This walkthrough guides you through using Contour

## Test in minikube

Start minikube:
```
minikube start
```

Install kapp-controller 0.20+
```
kubectl apply -f https://github.com/vmware-tanzu/carvel-kapp-controller/releases/latest/download/release.yml
```

Install the Package Metadata:
```
kubectl apply -f target/k8s
```

Install the Required RBAC for the package install (create the control NS):
```
kubectl apply -f target/test/packageinstall-ns-rbac.yaml
```

Create the configuration file for your cluster:
```
kubectl create secret generic contour -n contour-package --from-file=values.yaml=src/examples-values/minikube.yaml -o yaml --dry-run=client | kubectl apply -f -
```

Create the package:
```
kubectl apply -f target/test/packageinstall.yaml
```

Verify the installation:
```
watch kubectl get packageinstall -A
```

If there's an issue, you can verify the problem with:

```
kubectl get packageinstall contour -n contour-package -o yaml
```

## Develop checklist

1. Update your [config.json](./config.json) with the package info
2. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
3. Test your bundle: `ytt --data-values-file src/example-values/minikube.yaml  -f src/bundle/config` providing a sample values file from [example-values](./src/examples-values/)
4. Build your bundle `./hack/build.sh`
5. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)

**NOTE**: `develop` versions will not be image locked and will have a version of 0.0.0+develop to comply with semver.