Here are detailed answers to the **Argo CD Interview Questions** across basic, intermediate, advanced, and scenario-based levels:

---

### ✅ Basic Level

**1. What is Argo CD, and how does it differ from traditional CI/CD tools like Jenkins?**
Argo CD is a declarative GitOps continuous delivery tool for Kubernetes.

* **Key difference:** Jenkins is imperative and procedural, focused on running pipelines. Argo CD is declarative and synchronizes the Kubernetes cluster to the state defined in Git.

**2. What is GitOps? How does Argo CD implement GitOps principles?**
GitOps is a practice where Git is the single source of truth for infrastructure and app configurations.

* Argo CD syncs Kubernetes manifests from Git to clusters automatically or manually.
* It continuously monitors Git repos and applies the desired state.

**3. What are the key components of Argo CD?**

* **API Server:** Frontend for UI and CLI.
* **Repo Server:** Fetches and renders manifests.
* **Application Controller:** Reconciles desired vs actual state.
* **Dex (optional):** Handles authentication (OIDC).

**4. How do you define an application in Argo CD?**
An Application is a Kubernetes custom resource (`Application`) that specifies:

* The Git repo and path.
* The target cluster and namespace.
* Sync policies and tools (Helm, Kustomize, etc.).

**5. What are the different sync options in Argo CD?**

* **Manual Sync:** Triggered by a user.
* **Automatic Sync:** Syncs on every Git change.
* **Sync Strategies:** Hooks, waves, and retry policies.

**6. How does Argo CD detect drift in Kubernetes resources?**

* It compares live cluster resources against the Git state.
* If there’s a mismatch (e.g., manual change), status is marked **OutOfSync**.

**7. What config management tools does Argo CD support?**

* **Kustomize**
* **Helm**
* **Jsonnet**
* **Plain YAML**
* **Ksonnet (deprecated)**

**8. Explain the difference between ‘Synced’ and ‘OutOfSync’ status.**

* **Synced:** Cluster state matches Git.
* **OutOfSync:** Detected drift or changes not applied yet.

---

### 🔹 Intermediate Level

**1. How do you manage multiple environments (dev/staging/prod) using Argo CD?**

* Use separate folders or branches in Git.
* Create separate Argo CD Applications for each environment.
* Use Kustomize overlays or Helm values for environment-specific config.

**2. How does Argo CD handle secrets? What are the best practices?**

* Argo CD doesn’t encrypt secrets by default.
* Best practices:

  * Use tools like **Sealed Secrets**, **Vault**, **SOPS**, or **External Secrets Operator**.
  * Avoid storing plain secrets in Git.

**3. What is self-healing in Argo CD, and how does it work?**

* In auto-sync mode, Argo CD re-applies Git state if manual drift occurs.
* This ensures the cluster "heals" to match Git.

**4. Explain Argo CD’s RBAC system. How can you restrict access to a specific app or namespace?**

* Based on **roles and policies** defined in `argocd-rbac-cm`.
* Use rules like:
  `p, dev-user, applications, get, my-app/*, allow`
* You can bind roles to users/groups via OIDC.

**5. What happens if the Git repo is updated but the cluster is not?**

* Argo CD detects this and marks the app **OutOfSync**.
* You can manually or automatically sync.

**6. What is the difference between automatic and manual syncing in Argo CD?**

* **Manual:** User must trigger a sync.
* **Automatic:** Argo CD applies changes as soon as Git changes are detected.

**7. What tools or plugins can you use to secure or encrypt secrets in Git when using Argo CD?**

* **Sealed Secrets**
* **HashiCorp Vault**
* **SOPS (with GPG or KMS)**
* **External Secrets Operator**

---

### 🔹 Advanced Level

**1. How would you integrate Argo CD with a CI tool like GitHub Actions or Jenkins?**

* CI tool pushes manifest changes to Git.
* Argo CD auto-syncs those changes.
* Optional: Trigger a sync via Argo CD API/CLI from the CI tool post-deployment.

**2. How do you implement blue-green or canary deployments using Argo CD?**

* Use **Argo Rollouts** in conjunction with Argo CD.
* Argo CD deploys Rollout objects.
* Argo Rollouts controller manages traffic shifting and progress.

**3. Explain how Argo CD manages deployments to multiple clusters.**

* Register clusters via `argocd cluster add`.
* Application manifest defines target cluster.
* Argo CD uses context names to deploy accordingly.

**4. How can you audit who deployed what and when in Argo CD?**

* Enable **audit logging**.
* Use Argo CD UI or CLI to view **application history**.
* Integrate with **external logging systems** (e.g., ELK, Grafana Loki).

**5. How do you customize health checks and sync waves in Argo CD?**

* Define custom health checks in `argocd-cm` using Lua scripts.
* Use `syncWave` annotation (e.g., `argocd.argoproj.io/sync-wave: "1"`) to control the order of resource syncs.

---

### 📘 Scenario-Based Questions

**1. OutOfSync but no Git changes. How do you troubleshoot and resolve?**

* Check for manual changes (e.g., kubectl apply).
* Inspect live vs desired diff.
* Re-sync to restore desired state.
* Consider enabling self-heal mode.

**2. Deploy Helm chart from private Git repo. Steps:**

* Add repo to Argo CD with credentials (SSH or HTTPS token).
* Define Application manifest with repo URL, chart path, and target revision.
* Use Helm tool in the spec.

**3. Manual kubectl apply used. How does Argo CD respond?**

* Detects drift and marks as OutOfSync.
* In auto-sync mode: reverts manual change.
* Best practice: use RBAC/OPA/Gatekeeper to block kubectl changes.

**4. Managing multiple environments. How do you structure Git and Argo CD?**

* Git structure:

  ```
  ├── apps/
  │   ├── dev/
  │   ├── staging/
  │   └── prod/
  ```
* Use Argo CD Applications per env.
* Use separate clusters or namespaces.
* Enforce access control per environment.

**5. Security team needs audit trail. What to do?**

* Enable Argo CD audit logs.
* Send logs to centralized logging (e.g., ELK, Loki).
* Use Git history and Argo CD sync history for full traceability.

**6. Git push caused failure. How to roll back?**

* Use Argo CD UI or CLI to rollback to a previous sync revision.
* Optionally use Git revert and re-sync.

**7. Non-admin dev should deploy to dev only. How?**

* Create RBAC policy:

  ```
  p, dev-user, applications, sync, dev-namespace/*, allow
  ```
* Bind `dev-user` role via `argocd-rbac-cm`.
* Restrict access to only dev namespace.

**8. Managing across 3 clusters. How?**

* Use `argocd cluster add` to register each cluster.
* Define applications targeting each cluster by name/context.
* Use labels to categorize apps by cluster.

---

Would you like me to prepare a downloadable cheat sheet PDF or flashcards for easy review?
