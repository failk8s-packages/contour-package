# contour-package

This package provides contour functionality using [projectcontour.io](https://projectcontour.io/).

## Components

- Project contour

## Configuration

The following configuration values can be set to customize the project contour installation.

### Global

| Value                                    | Required/Optional | Default              | Description                                                                                                                                                                             |
| ---------------------------------------- | ----------------- | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `infraProvider`                          | Required          | **custom**           | Infrastucture provider hosting the cluster. Possible values are `aws`, `gcp`, `azure`, `kind`, `minikube` and `custom`. This will predefine some values for the envoy svc and daemonset |
| `namespace`                              | Required          | **projectcontour**   | The namespace in which to deploy contour                                                                                                                                                |
| `createNamespace`                        | Required          | **True**             | Needs the namespace where contour will be installed to be created?                                                                                                                      |
| `service.type`                           | Required          | **LoadBalancer**     | Type of Service for Envoy service. (ClusterIP or LoadBalancer)                                                                                                                          |
| `service.useHostPorts`                   | Required          | **true**             | Whether hostPorts should be configured on the daemonset                                                                                                                                 |
| `externaldns.domains`                    | Optional          | <EMPTY>              | List of DNS domains that will be used to configure external dns. (A wildcard `*.` preffix and `.` suffix will be added                                                                  |
| `configFileContents.defaultHttpVersions` | Optional          | "HTTP/1.1", "HTTP/2" | HTTP protocols to use. You can provide only one of them to disable the other                                                                                                            |

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
