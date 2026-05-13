Users
  ↓
AWS ALB Ingress
  ↓
EKS Cluster (Terraform created)
  ↓
Pods (App deployed via ArgoCD + Helm)

CI/CD:
GitHub / Jenkins → Docker Image → Registry

CD:
ArgoCD → Sync Git repo → Deploy to Kubernetes

Monitoring:
Prometheus → Metrics
Grafana → Dashboards




Final Answer
Your GitHub Actions Pipeline

With GitOps + Argo CD:

✅ No EKS authentication needed in pipeline
✅ No kubeconfig needed
✅ No kubectl needed
✅ No OIDC needed for deployment
✅ No aws configure needed

But ArgoCD Needs Cluster RBAC
Because ArgoCD itself performs deployment inside EKS.
That is normal and automatic after installation.



ArgoCD GitOps Deployment Notes
-----------------------------------------------------------------------------------------
Most Common Real Setup
Usually companies simply do:

kubectl create namespace argocd
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


Install ArgoCD
==============================
For most real-world beginner/intermediate GitOps setups, this is enough:

kubectl create namespace argocd
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


ArgoCD already includes:
==============================
- RBAC
- Service Accounts
- Controllers
- Reconciliation Logic
- Sync Engine


Rollback in GitOps
==============================
You do NOT need to write rollback code inside GitHub Actions when using:
GitHub Actions + ArgoCD + GitOps

Because rollback becomes:
Git rollback

instead of:
kubectl rollout undo


Example
==============================
Old image:
bankapp:v1

New bad image:
bankapp:v2

GitOps repo changed to:
image: bankapp:v2


Rollback Process
==============================
Just revert the Git commit:

git revert <commit-id>
git push

Then ArgoCD automatically syncs back to:
image: bankapp:v1
This IS the rollback.


Why GitOps Rollback Is Better
==============================

Traditional Deployment:
------------------------------
kubectl rollout undo


GitOps Deployment:
------------------------------
git revert


GitOps Advantages
==============================
- Fully auditable
- Version controlled
- Safer
- Cleaner
- Easier recovery
- Easier production tracking


Modern Enterprise Reality
==============================
Most companies using:
- ArgoCD
- FluxCD

usually DO NOT write rollback scripts in CI pipelines.
They simply:

- revert Git
OR
- rollback from ArgoCD UI


ArgoCD Built-in Rollback Features
==============================
ArgoCD UI allows you to:
- View deployment history
- Select older revision
- Rollback with one click


Final Modern GitOps Architecture
==============================
GitHub Actions
      ↓
Build / Test / Scan
      ↓
Push Docker Image
      ↓
Update GitOps Repo
      ↓
ArgoCD Sync
      ↓
EKS Deployment
