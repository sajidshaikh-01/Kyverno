# Kyverno Mutating Policy – Production Guide

## 1. What is a Mutating Policy?

A **Mutating Policy** in Kyverno is used to **automatically modify (mutate) Kubernetes resources** when they are created or updated.

Instead of **blocking** a resource (like validating policies), mutating policies **fix the resource automatically** so it complies with platform and security standards.

### Simple Definition

> A mutating policy auto-corrects Kubernetes manifests before they are stored in the cluster.

---

## 2. Where Mutating Policies Run

Mutating policies run during the **Kubernetes Admission Control phase**, just like validating policies.

### Request Flow

1. Developer runs `kubectl apply`
2. Request reaches Kubernetes API Server
3. Kyverno Mutating Webhook is triggered
4. Kyverno modifies the resource
5. Modified resource is stored in etcd

The developer does **not** need to change their YAML manually.

---

## 3. Why Mutating Policies Are Used in Production

In production environments:

* Developers move fast
* Perfect YAMLs are unrealistic
* Strict blocking slows delivery

Mutating policies allow:

* **Developer freedom**
* **Platform consistency**
* **Security by default**

They are widely used by **Platform Engineering and SRE teams**.

---

## 4. Common Production Use Cases

### 4.1 Auto-Add CPU & Memory Limits

**Problem:**

* Developers forget resource limits

**Mutating Solution:**

* Kyverno automatically injects default CPU and memory limits

---

### 4.2 Inject Mandatory Labels & Annotations

**Problem:**

* Missing labels break monitoring, billing, and ownership tracking

**Mutating Solution:**

* Kyverno auto-adds standard labels like `app`, `team`, `environment`

---

### 4.3 Enforce SecurityContext Defaults

**Problem:**

* Containers run with insecure defaults

**Mutating Solution:**

* Automatically add:

  * `runAsNonRoot: true`
  * `allowPrivilegeEscalation: false`

---

### 4.4 Add Image Pull Policy

**Problem:**

* Missing `imagePullPolicy` causes unexpected behavior

**Mutating Solution:**

* Inject `IfNotPresent` or `Always`

---

### 4.5 Environment-Specific Defaults

**Problem:**

* Dev and prod need different configurations

**Mutating Solution:**

* Apply different defaults based on namespace or labels

---

## 5. How Mutating Policies Work (Conceptually)

Kyverno uses **patching mechanisms** to update incoming manifests:

* Strategic merge patch
* JSON patch

The mutation happens **before validation and persistence**.

---

## 6. Mutating vs Validating Policies (Production View)

| Feature              | Mutating Policy | Validating Policy |
| -------------------- | --------------- | ----------------- |
| Purpose              | Auto-fix        | Allow / Deny      |
| Blocks requests      | ❌ No            | ✅ Yes             |
| Developer friction   | Low             | High              |
| Used for defaults    | ✅ Yes           | ❌ No              |
| Security enforcement | Medium          | High              |

---

## 7. Production Best Practices

1. **Use mutate for defaults**

   * Resource limits
   * Labels
   * SecurityContext

2. **Combine with validating policies**

   * Mutate first
   * Validate critical rules

3. **Namespace-based mutation**

   * Different defaults for dev, stage, prod

4. **Keep mutations predictable**

   * Avoid complex logic
   * Document mutations clearly

---

## 8. Real Production Scenario

### Scenario

* EKS cluster
* ArgoCD GitOps pipeline
* Multiple dev teams

### Setup

* Mutating policies auto-add:

  * CPU/memory limits
  * Standard labels
  * Non-root security context

### Outcome

* Developers deploy faster
* Platform standards enforced
* No manual YAML reviews

---

## 9. When NOT to Use Mutating Policies

Avoid mutating policies when:

* Security rule must never be bypassed (use validate)
* Mutation hides serious misconfiguration
* Developers must explicitly choose values

---

