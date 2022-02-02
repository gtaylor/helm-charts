# tailscale-subnet-router

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: v1.20](https://img.shields.io/badge/AppVersion-v1.20-informational?style=flat-square)

Deploy a Tailscale subnet router on Kubernetes

**Homepage:** <https://github.com/gtaylor/helm-charts/charts/tailscale-subnet-router>

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| Greg Taylor | greg@gctaylor.com | https://gc-taylor.com |

## Source Code

* <https://github.com/gtaylor/helm-charts/charts/tailscale-subnet-router>

## Prerequesites

Before installing this Chart, you'll need the following:

1. A Tailscale account.
2. A reusable [auth key](https://tailscale.com/kb/1085/auth-keys/).
3. A Kubernetes Secret in the Namespace that you'll installing this Chart in.
4. Your own Docker image built from the Dockerfile found [here](https://github.com/tailscale/tailscale/tree/main/docs/k8s).
5. It's probably worth briefly reading the upstream [Subnet + Kubernetes](https://github.com/tailscale/tailscale/tree/main/docs/k8s#subnet-router) instructions from which this Chart was based on.

## A note on secrets

This Chart does not expose a value to pass your Tailscale auth keys in, as the author populates these via other means (the Vault CSI driver).

Whether you manually create it or sync it in via other means (Vault, Sealed Secrets, etc), a Secret will need to exist in your target namespace before installing the Chart.

Unless overridden, the Chart will assume that you have a Secret named `tailscale-subnet-router-secrets` whose `AUTH_KEY` key contains your router's Tailscale auth key.

## A note on Docker images

Due to the sensitivity of this component, no Docker image is referenced or provided. We recommend building the upstream Dockerfile found [here](https://github.com/tailscale/tailscale/tree/main/docs/k8s) and pushing it somewhere. **Make sure to set the `image.repository` value accordingly.**

## Installing the Chart

To install the chart with the release name `my-release`:

```console
$ helm repo add gtaylor https://gtaylor.github.io/helm-charts
$ helm install my-release gtaylor/tailscale-subnet-router \
    --set image.repository your-repo-here.com/tailscale/tailscale-subnet-proxy \
```

If all goes well, you should see a new Subnet router in your Tailscale admin page.

**Note: You'll need to enable the routes and disable key expiry on the newly created host entry in the Tailscale admin page.**

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

**Tip: Make sure to revoke your auth key in Tailscale afterwards.**

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | [Affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) for pod assignment |
| fullnameOverride | string | `""` | Optional override for app fullname |
| image.pullPolicy | string | `"Always"` |  |
| image.repository | string | `nil` | **Build your own image and override this!** |
| image.tag | string | `"latest"` |  |
| imagePullSecrets | list | `[]` | List the secrets containing the Docker creds for images in this Chart |
| nameOverride | string | `""` | Optional override for app name |
| nodeSelector | object | `{}` | Node labels for [pod assignment](https://kubernetes.io/docs/user-guide/node-selection/) |
| podAnnotations | object | `{}` | Additional annotations for the pods |
| podSecurityContext | object | `{}` |  |
| replicas | int | `1` | Do not change! Only `1` is currently supported. |
| resources | object | `{}` | Resources to allocate to the pods |
| securityContext.runAsGroup | int | `1000` | The GID of the user to run the router as |
| securityContext.runAsUser | int | `1000` | The UID of the user to run the router as |
| serviceAccount | object | `{"annotations":{},"create":true,"name":""}` | The service account to create or attach |
| tailscale.auth.secretKey | string | `"AUTH_KEY"` | The key within the above Secret that contains a Tailscale auth key |
| tailscale.auth.secretName | string | `"tailscale-subnet-router-secrets"` | The name of the secret containing a Tailscale auth key |
| tailscale.routes | list | `["10.96.0.0/12","10.244.0.0/16"]` | Routes for the subnet router to publish |
| tailscale.state.secretName | string | `"tailscale-subnet-router-state"` | The secret that the subnet router will store its state in |
| tolerations | list | `[]` | [Tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) for pod assignment |
| volumeMounts | list | `[]` | Additional volumes to add to mount to the primary container |
| volumes | list | `[]` | Additional volumes to add to the pod |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.7.0](https://github.com/norwoodj/helm-docs/releases/v1.7.0)
