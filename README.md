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
2. Update your [vendir.yml](./src/bundle/vendir.yml) with your upstreams
3. `vendir sync` in `src/bundle` to fetch your new upstream files
4. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
5. Update your [bundle.yml](./src/bundle/.imgpkg/bundle.yml) file
6. Test your bundle: `ytt -f src/bundle/config`
7. Lock images used: `kbld -f src/bundle --imgpkg-lock-output src/bundle/.imgpkg/images.yml`
8. Publish your bundle: `imgpkg push --bundle quay.io/failk8s/<NAME>-package:<VERSION> --file src/bundle`. These steps can be done via [hack/build-package.sh](./hack/build-package.sh)
9. Package up your k8s manifests and test in k8s [hack/package-manifests.sh](./hack/package-manifests.sh). The files will be in `target` folder.