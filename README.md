# charts

Helm charts, published as a Helm repository on GitHub Pages.

```sh
helm repo add dawidd6 https://dawidd6.github.io/charts
```

| Chart | Install |
| ----- | ------- |
| [dynacat](dynacat) | `helm install dynacat dawidd6/dynacat` |

Pushes to `master` release changed charts with [chart-releaser](https://github.com/helm/chart-releaser-action): each new version gets a `<chart>-<version>` GitHub release, and `index.yaml` on the `gh-pages` branch lists them.
Renovate updates image tags and bumps the chart version with them.
