Got it 👍 You want **detailed notes on Argo CD Rollouts**. Let me give you a structured breakdown that will help you both in **understanding** and in **interviews**.

---

# 📘 Detailed Notes on **Argo Rollouts**

---

## 🔹 1. What is Argo Rollouts?

* **Argo Rollouts** is a **Kubernetes controller + CRD** (Custom Resource Definition) that provides advanced deployment strategies.
* Works as an **alternative to Deployment** object in Kubernetes.
* Supports **progressive delivery** (gradual rollout, traffic shifting, automated analysis).
* **Integrates with Argo CD** for GitOps workflows.

---

## 🔹 2. Why do we need Argo Rollouts?

* Standard Kubernetes **Deployment** only supports:

  * **RollingUpdate**
  * **Recreate**
* In **production**, teams need safer deployment methods:

  * **Canary** (gradual rollout to subset of users)
  * **Blue-Green** (two environments, switch traffic)
  * Automated **metrics checks** before promoting rollout.
* Argo Rollouts solves this with **advanced strategies**.

---

## 🔹 3. Components of Argo Rollouts

1. **Rollout CRD**

   * Replaces `Deployment` object.
   * Defines the strategy (blue-green, canary, etc.).

2. **ReplicaSets**

   * Like Deployment, Rollouts manage ReplicaSets for versions.

3. **Analysis**

   * Automates checking of app health using Prometheus, Kayenta, Datadog, etc.

4. **Traffic Routing Plugins**

   * Works with:

     * Ingress Controller (NGINX, ALB, GKE Ingress)
     * Service Mesh (Istio, Linkerd, App Mesh, SMI)

5. **Argo Rollouts Controller**

   * Runs in the cluster.
   * Ensures rollout steps happen as defined.

6. **kubectl argo rollouts plugin**

   * CLI to visualize rollout status, promote, abort, retry, etc.
     Example:

   ```bash
   kubectl argo rollouts get rollout <rollout-name>
   kubectl argo rollouts promote <rollout-name>
   ```

---

## 🔹 4. Deployment Strategies in Argo Rollouts

### 1. **Blue-Green**

* Two environments:

  * **Active (Blue)**: current version
  * **Preview (Green)**: new version
* Traffic switches from Blue → Green when validated.
* Supports **previewService** and **activeService**.

✅ Use case: Fast rollback, zero downtime, simple strategy.

---

### 2. **Canary**

* Gradually shifts traffic from old → new version in **steps**.
* Example steps:

  ```yaml
  steps:
  - setWeight: 20
  - pause: { duration: 5m }
  - setWeight: 50
  - pause: { duration: 10m }
  - setWeight: 100
  ```
* Integrates with **metrics analysis** (auto promote or rollback).
* Safer for **high-risk changes**.

✅ Use case: Production apps with need for gradual rollout.

---

### 3. **Progressive Canary with Analysis**

* Canary rollout + automated metrics check.
* Example: Only promote if error rate < 1% in Prometheus.

✅ Use case: Automated production safety.

---

### 4. **Experiment**

* Deploy multiple versions at the same time.
* Run tests / collect metrics to decide winner.

✅ Use case: A/B testing, ML model validation.

---

## 🔹 5. Argo Rollouts Workflow with Argo CD (GitOps)

1. **Push code** → CI builds image → updates Git repo.
2. **Argo CD syncs** Git repo → applies Rollout manifest.
3. **Argo Rollouts controller** executes rollout steps.
4. **Traffic shifting** via ingress/service mesh.
5. **Analysis runs** → success = promote, failure = rollback.

---

## 🔹 6. Features

* **Advanced Deployment Strategies**: Canary, Blue-Green, Experiments.
* **Traffic Shifting**: via ingress/service mesh.
* **Automated Rollback**: if analysis fails.
* **Pause/Resume/Promote/Abort** rollouts.
* **Dashboard**: Argo CD UI integration.
* **CLI**: `kubectl argo rollouts` plugin.

---

## 🔹 7. Example Rollout YAML

### Canary Rollout

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: my-app
spec:
  replicas: 5
  strategy:
    canary:
      steps:
      - setWeight: 20
      - pause: { duration: 5m }
      - setWeight: 50
      - pause: { duration: 10m }
      - setWeight: 100
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: myrepo/myapp:v2
```

---

## 🔹 8. Comparison: Argo Rollouts vs Kubernetes Deployment

| Feature            | Kubernetes Deployment   | Argo Rollouts                               |
| ------------------ | ----------------------- | ------------------------------------------- |
| Strategies         | RollingUpdate, Recreate | Blue-Green, Canary, Experiment, Progressive |
| Traffic Shifting   | Basic                   | Weighted traffic (Ingress/Service Mesh)     |
| Automated Analysis | ❌                       | ✅                                           |
| Rollback           | Manual                  | Automated                                   |
| GitOps Integration | Basic                   | Strong with Argo CD                         |

---

## 🔹 9. Best Practices (Prod)

* Always integrate with **monitoring** (Prometheus/Datadog).
* Use **pause** + **manual promote** in high-risk deployments.
* For microservices:

  * **Blue-Green** for infra changes.
  * **Canary** for app features.
* Use **Argo CD + Rollouts** together for GitOps.

---

✅ **Summary**:
Argo Rollouts = Safer deployments in Kubernetes.
Best for **progressive delivery in production** with GitOps.

---

Do you want me to also give you **interview Q\&A** specifically on Argo Rollouts (like "difference between Argo Rollouts and Argo CD", "how rollback works") so you can prepare better?
Exactly 👍 You got it right.

When you use **Argo Rollouts**, you **don’t need a Kubernetes Deployment YAML** for that app, because:

* A **Rollout CRD** (Custom Resource) is basically an **enhanced Deployment**.
* It manages **ReplicaSets** just like Deployment does, but with **extra features** (canary, blue-green, experiments, traffic shifting, analysis, etc.).

---

### 🔹 Difference in simple terms:

| **Kubernetes Deployment**           | **Argo Rollout**                                           |
| ----------------------------------- | ---------------------------------------------------------- |
| Standard object (`Deployment`)      | Custom object (`Rollout`)                                  |
| Strategies: RollingUpdate, Recreate | Strategies: Canary, Blue-Green, Experiment + RollingUpdate |
| No traffic shifting                 | Weighted traffic shifting (Ingress/Service Mesh)           |
| No automated analysis               | Built-in Analysis + Rollback                               |
| Basic YAML                          | Extended YAML with rollout steps                           |

---

### 🔹 Example:

If you were using a **Deployment**:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: myrepo/myapp:v1
```

With **Argo Rollouts**, you replace it with:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: my-app
spec:
  replicas: 3
  strategy:
    canary:
      steps:
      - setWeight: 30
      - pause: { duration: 2m }
      - setWeight: 60
      - pause: { duration: 5m }
      - setWeight: 100
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: myrepo/myapp:v2
```

---

👉 So in production, **you either use Deployment OR Rollout**, not both, for the same app.

⚠️ One caveat:

* If you still want **simple apps with no advanced strategies**, you can continue using **Deployment**.
* But if you want **canary/blue-green**, switch that Deployment → Rollout.

---

Would you like me to also show you **how Rollout connects with a Service** (because unlike Deployment, Rollout usually needs **activeService / previewService** for blue-green or ingress for canary)?
Got it 👍 You just want the **differences between a Deployment YAML and an Argo Rollout YAML** (not the full theory). Here’s the crisp comparison:

---

### 🔹 Key Differences in YAML

| **Deployment YAML**                            | **Argo Rollout YAML**                                                                                 |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `apiVersion: apps/v1`                          | `apiVersion: argoproj.io/v1alpha1`                                                                    |
| `kind: Deployment`                             | `kind: Rollout`                                                                                       |
| `spec.strategy.type: RollingUpdate / Recreate` | `spec.strategy.canary / blueGreen / experiment`                                                       |
| No `steps` support                             | Supports `steps` (setWeight, pause, analysis, etc.)                                                   |
| Traffic handled by default Service             | Traffic shifting via `activeService` / `previewService` (Blue-Green) or ingress/service mesh (Canary) |
| Rollback is manual                             | Rollback can be **automated with Analysis**                                                           |
| Simpler spec                                   | Extended spec with rollout strategies                                                                 |

---

### 🔹 Example Snippets

**Deployment:**

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
```

**Argo Rollout:**

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
spec:
  replicas: 3
  strategy:
    canary:
      steps:
      - setWeight: 30
      - pause: { duration: 2m }
```

---

👉 In short:

* **Only these 3 main fields differ:** `apiVersion`, `kind`, and `strategy`.
* Rollouts add **extra fields (steps, services, analysis)** on top of Deployment.

---

Do you want me to also make a **side-by-side full YAML comparison** (Deployment vs Rollout for the same app) so you can visually see the differences line by line?
