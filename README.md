# ArgoCD Manage Applications — *Open Source* & Stable `@v1`

**Handles full ArgoCD lifecycle:** authenticate, connect, delete, create, sync, and wait for sync completion.  
Supports *no-fail mode* for restore cluster runs.  
Used in both **K8s-deploy** and **K8s-restore-cluster** workflows.

---

## 🧩 Overview

This composite GitHub Action automates the full lifecycle of ArgoCD applications directly from a workflow.  
It can:
- Authenticate using token or username/password
- Create, delete, or re-sync ArgoCD applications
- Wait for applications to reach a healthy synced state
- Support idempotent restore scenarios

---

## 🌐 GitOps Manager™ Enterprise Platform

[**GitOps Manager™ Enterprise**](https://gitopsmanager.io) is the full platform that powers this open-source workflow.  
It’s a **turnkey GitOps automation platform** for AWS and Azure — combining open-source GitHub Actions, Kubernetes infrastructure automation, and global-scale CI/CD.

**Highlights:**
- Secure, opinionated **multi-cloud GitOps automation** for Kubernetes workloads.  
- Deep integration with **ArgoCD**, **Argo Workflows**, **Traefik**, **ECK**, and **Kubernetes Dashboard**.  
- Built for **high availability**, **autoscaling**, and **managed upgrades**.  
- Supports **Workload Identity**, **Pod Identity**, and **private, network-isolated clusters**.  
- Enables **global deployments**, **secret management**, and **production-grade infrastructure** with **zero vendor lock-in**.

🔗 Learn more: [https://gitopsmanager.io](https://gitopsmanager.io)

---


## 🧩 Dependencies

This action includes a bundled copy of **nunjucks.js** for JSON template rendering.

- **Library:** [nunjucks](https://github.com/mozilla/nunjucks)
- **Version:** 3.2.4
- **License:** BSD-2-Clause
- **Copyright:** © Mozilla Foundation

Used under the terms of the BSD-2-Clause license.  
See the [nunjucks LICENSE file](https://github.com/mozilla/nunjucks/blob/main/LICENSE) for full details.

---

## 🚀 Inputs

| Name | Description | Required | Default |
|------|--------------|-----------|----------|
| `argocd_url` | Full ArgoCD base URL (e.g. `https://aks-prod-weu-argocd-argocd-web-ui.affinity7software.com`) | ✅ Yes | — |
| `argocd_auth_token` | Optional ArgoCD API token | ❌ No | — |
| `argocd_username` | ArgoCD username (used if token not provided) | ❌ No | — |
| `argocd_password` | ArgoCD password (used if token not provided) | ❌ No | — |
| `insecure_argo` | Set to `true` to skip SSL verification (`curl -k`) | ❌ No | `false` |
| `argocd_ca_cert` | Base64 or plain text CA certificate | ❌ No | — |
| `namespace` | Kubernetes namespace for applications | ✅ Yes | — |
| `cd_repo` | Git repo containing the ArgoCD application manifests | ✅ Yes | — |
| `cd_path_rel` | Relative path inside the CD repo where app files are located (e.g. `<cluster>/<namespace>`) | ✅ Yes | — |
| `cd_repo_org` | Organization name for the CD repo | ✅ Yes | — |
| `overlay_dir` | Overlay directory (if applicable) | ❌ No | `""` |
| `apps` | JSON array of `{name, path, images}` defining apps to manage | ✅ Yes | — |
| `delete_first` | If `true`, delete apps before recreating them | ❌ No | `false` |
| `delete_only` | If `true`, only delete apps (no recreate/sync) | ❌ No | `false` |
| `skip_status_check` | Skip waiting for sync and health check | ❌ No | `false` |

---

## 🧠 Usage Example

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: ArgoCD Manage Applications
        uses: ./.github/actions/argocd-manage
        with:
          argocd_url: ${{ secrets.ARGOCD_URL }}
          argocd_auth_token: ${{ secrets.ARGOCD_TOKEN }}
          namespace: production
          cd_repo: affinity7software/k8s-apps
          cd_path_rel: clusters/prod
          cd_repo_org: affinity7software
          apps: '[{"name":"frontend","path":"frontend","images":[]},{"name":"backend","path":"backend","images":[]}]'
          delete_first: false
          delete_only: false
```

---

## ⚙️ Behavior Summary

| Phase | Description |
|--------|--------------|
| **Auth** | Determines authentication mode (token or basic) |
| **Connect** | Obtains valid ArgoCD token and CA trust config |
| **Delete** | Deletes apps if flagged (`delete_first` or `delete_only`) |
| **Create** | Renders and posts new ArgoCD app manifests |
| **Sync** | Triggers sync with force + prune |
| **Wait** | Waits for apps to reach `Synced` and `Healthy` state |

---

## 🛠️ Integration

This action is **used in:**
- [`K8s-deploy`](../workflows/k8s-deploy.yml)
- [`K8s-restore-cluster`](../workflows/k8s-restore-cluster.yml)

It enables both workflows to reliably orchestrate ArgoCD app deployments and recovery sequences.

---

## 🔢 Versioning Policy — Official Release

Starting with this release, all `v1` versions follow the same **stable tagging model** used across GitOps Manager™ Actions.

| Tag | Moves When | Purpose |
|------|-------------|----------|
| **`v1`** | Any new release in the `v1.x.x` series | Always points to the latest stable release (no breaking changes). |
| **`v1.3`** | New patch within that feature line (e.g. `v1.3.5 → v1.3.6`) | Tracks bug fixes and improvements only — no new required inputs. |
| **`v1.3.7`** | Never | Fully immutable, reproducible snapshot. |

All tags will now **increment forward permanently** — no re-use or re-tagging of old versions. 

---

## 📄 License

MIT License © GitOps Manager, S.L.
