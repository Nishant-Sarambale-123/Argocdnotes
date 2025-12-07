Here are **Argo CD All Commands Notes** — clean, categorized, and interview-friendly.

---

# 🚀 **Argo CD – All Important Commands (Notes)**

(full cheat-sheet, simple + practical)

---

# 📌 **1. Argo CD Login / Context Commands**

### **Login to Argo CD**

```bash
argocd login <ARGOCD_SERVER> --username admin --password <PASSWORD> --insecure
```

### **Check current Argo CD context**

```bash
argocd context
```

### **List available contexts**

```bash
argocd context list
```

### **Logout**

```bash
argocd logout <ARGOCD_SERVER>
```

---

# 📌 **2. Application CRUD Commands**

### **Create App**

```bash
argocd app create myapp \
  --repo https://github.com/nish/repo.git \
  --path manifests \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

### **Update an App (e.g., change repo/path)**

```bash
argocd app set myapp --path newpath
```

### **Get App Details**

```bash
argocd app get myapp
```

### **List all Apps**

```bash
argocd app list
```

### **Delete App**

```bash
argocd app delete myapp --cascade
```

---

# 📌 **3. Sync Commands (VERY Important in Interviews)**

### **Sync the Application**

```bash
argocd app sync myapp
```

### **Sync with options**

```bash
argocd app sync myapp --prune --timeout 300
```

### **Auto-sync enable**

```bash
argocd app set myapp --sync-policy automated
```

### **Auto sync with prune & self-heal**

```bash
argocd app set myapp \
  --sync-policy automated \
  --self-heal \
  --auto-prune
```

### **Refresh app state**

```bash
argocd app refresh myapp
```

---

# 📌 **4. Diff Commands (Compare Git vs Cluster)**

### **Show difference**

```bash
argocd app diff myapp
```

### **Diff with local manifests**

```bash
argocd app diff myapp --local ./manifests
```

---

# 📌 **5. Rollout / History Commands**

### **View history**

```bash
argocd app history myapp
```

### **Rollback to a previous version**

```bash
argocd app rollback myapp <REVISION>
```

---

# 📌 **6. App Logs & Events**

### **Get app logs (for resource)**

```bash
argocd app logs myapp --kind Pod --name myapp-12345
```

### **Get events**

```bash
argocd app events myapp
```

---

# 📌 **7. Repository Commands**

### **List git repos**

```bash
argocd repo list
```

### **Add repo**

```bash
argocd repo add https://github.com/nish/repo.git --username user --password pass
```

### **Remove repo**

```bash
argocd repo rm https://github.com/nish/repo.git
```

---

# 📌 **8. Project Commands (Argo CD App Projects)**

### **List Projects**

```bash
argocd proj list
```

### **Create Project**

```bash
argocd proj create myproject
```

### **Add permitted namespace**

```bash
argocd proj add-namespace myproject mynamespace
```

### **Get project details**

```bash
argocd proj get myproject
```

### **Delete project**

```bash
argocd proj delete myproject
```

---

# 📌 **9. Cluster Management Commands**

### **List clusters connected to Argo CD**

```bash
argocd cluster list
```

### **Add a new cluster**

```bash
argocd cluster add <CONTEXT_NAME>
```

### **Remove a cluster**

```bash
argocd cluster rm <SERVER>
```

---

# 📌 **10. Admin & User Commands**

### **Add a new user (Local users)**

```bash
argocd account create <username>
```

### **Change password**

```bash
argocd account update-password
```

### **List accounts**

```bash
argocd account list
```

---

# 📌 **11. Application Resource Operations**

### **List resources inside app**

```bash
argocd app resources myapp
```

### **Terminate operation**

```bash
argocd app terminate-op myapp
```

### **Suspend app**

```bash
argocd app suspend myapp
```

### **Resume app**

```bash
argocd app resume myapp
```

---

# 📌 **12. Troubleshooting Commands**

### **Check app health**

```bash
argocd app get myapp --hard-refresh
```

### **Force sync**

```bash
argocd app sync myapp --force
```

### **Retry sync**

```bash
argocd app retry myapp
```

---

# 📌 **13. App of Apps Deployment Commands**

### **Create parent app**

```bash
argocd app create root-app \
  --repo https://github.com/nish/argo-apps.git \
  --path root \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace argocd \
  --sync-policy automated
```

---

# 📌 **14. Helm with Argo CD**

### **Create Argo CD app using Helm chart**

```bash
argocd app create myhelmapp \
  --repo https://github.com/nish/helm.git \
  --path chart \
  --helm-set image.tag=v1
```

### **Pass multiple helm values**

```bash
argocd app create myapp \
  --helm-set key1=val1 \
  --helm-set key2=val2
```

---

# 📌 **15. Kustomize with Argo CD**

```bash
argocd app create mykustomapp \
  --repo https://github.com/nish/repo.git \
  --path overlays/dev \
  --dest-namespace dev
```

---

# 💡 **Quick FAQ**

### ❓ **Self-heal = ?**

If someone changes deployment manually in cluster → Argo CD automatically fixes it back to Git.

### ❓ **Prune = ?**

If you delete resource in Git → Argo CD deletes it from cluster.

---

# ✔️ Need PDF / One-page printable sheet?

I can generate a clean PDF or condensed notes version.
