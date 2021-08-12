# contour-package

This package provides contour functionality using [projectcontour.io](https://projectcontour.io/).

## Components

* Project contour

## Configuration

The following configuration values can be set to customize the project contour installation.

### Global

| Value | Required/Optional | Description |
|-------|-------------------|-------------|
| `namespace` | Optional | The namespace in which to deploy contour. |
| `domain` | Required | The main DNS domain in which contour will be registered. This is to support external_dns autoregistration |
| `wildcard_domain` | Required | The applications wildcard DNS domain in which contour will be registered. This is to support external_dns autoregistration |
| `privileged_clusterrole_name` | Optional | If PSPs are enabled on the cluster, this is the name of the privileged clusterrole that allows Envoy to start |

## Usage Example

This walkthrough guides you through using Contour


## Develop checklist


1. Update your [config.json](./config.json) with the package info
2. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
3. Test your bundle: `ytt --data-values-file src/example-values/minikube.yaml  -f target/bundle/config` providing a sample values file from [example-values](./src/examples-values/)
4. Build your bundle `./hack/build.sh`
5. Publish your bundle: `./hack/push.sh`
6. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)
