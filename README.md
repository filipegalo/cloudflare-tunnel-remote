# cloudflare-tunnel-remote

A Helm chart for running [`cloudflared`](https://github.com/cloudflare/cloudflared) as a
remotely-managed Cloudflare Tunnel connector inside a Kubernetes cluster.

## Installing

```bash
helm install my-tunnel . -f my-values.yaml
```

## Configuring the tunnel token

The tunnel needs a `TUNNEL_TOKEN` to authenticate to Cloudflare. There are two ways to supply it.

### Option 1: Let the chart manage the Secret (default)

Set `cloudflare.tunnel_token` and the chart creates a Secret on your behalf:

```yaml
cloudflare:
  tunnel_token: "<your-cloudflare-tunnel-token>"
```

```bash
helm install my-tunnel . --set cloudflare.tunnel_token=<your-cloudflare-tunnel-token>
```

### Option 2: Reference an externally managed Secret

If you manage the tunnel token Secret outside of this chart (e.g. via a separate secrets
pipeline), point the chart at it instead. When `cloudflare.existingSecret.name` is set, the
chart will not create its own Secret, and `cloudflare.tunnel_token` is ignored.

```yaml
cloudflare:
  existingSecret:
    name: cloudflare-tunnel-token
    key: tunnelToken # optional, defaults to "tunnelToken"
```

The existing Secret must contain the token under the configured key, e.g.:

```bash
kubectl create secret generic cloudflare-tunnel-token \
  --from-literal=tunnelToken=<your-cloudflare-tunnel-token>
```

```bash
helm install my-tunnel . \
  --set cloudflare.existingSecret.name=cloudflare-tunnel-token \
  --set cloudflare.existingSecret.key=tunnelToken
```

## Values

| Key                            | Description                                                                 | Default        |
| ------------------------------- | ---------------------------------------------------------------------------- | -------------- |
| `cloudflare.tunnel_token`       | Cloudflare Tunnel token used to create a chart-managed Secret.               | `""`           |
| `cloudflare.existingSecret.name`| Name of an existing Secret to use instead of the chart-managed one.          | `""`           |
| `cloudflare.existingSecret.key` | Key within the existing Secret that holds the tunnel token.                  | `tunnelToken`  |

See [`values.yaml`](values.yaml) for the full list of configurable values.
