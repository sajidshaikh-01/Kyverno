# Kyverno Validating Policy – Production Guide

## 1. What is a Validating Policy?

A **Validating Policy** in Kyverno is used to **allow or deny Kubernetes resources** based on defined rules **before they are created or updated** in the cluster.

In simple terms:

> A validating policy acts like a **security gate** that checks whether a resource follows rules. If the rules fail, the request is **blocked**.

Validating policies do **not modify** resources — they only **inspect and decide**.

---

## 2. Where Validating Policies Run

Validating policies run during the **Kubernetes Admission Control phase**.

Flow:

1. Developer runs `kubectl apply`
2. Request reaches Kubernetes API Server
3. Kyverno validating webhook is triggered
4. Policy rules are evaluated
5. Result:

   * ✅ Request allowed
   * ❌ Request denied with clear error message

This happens **before the resource is stored in etcd**.

---

## 3. Why Validating Policies Are Used in Production

In production Kubernetes clusters:

* Multiple teams deploy workloads
* Human errors are common
* Security risks are high

Validating policies ensure **bad configurations never enter the cluster**.

They are one of the **most important policy types** used in real production environments.

---

## 4. Common Production Use Cases

### 4.1 Enforcing Resource Limits (Cost Control)

**Problem:**

* Pods deployed without CPU/memory limits
* Causes node exhaustion and high cloud bills

**Validating Policy Solution:**

* Block pods without `resources.requests` and `resources.limits`

---

### 4.2 Prevent Running Containers as Root (Security)

**Problem:**

* Containers running as root increase attack surface

**Validating Policy Solution:**

* Deny pods where `runAsNonRoot: true` is missing

---

### 4.3 Blocking `latest` Image Tag (Stability)

**Problem:**

* `latest` tag causes unpredictable deployments

**Validating Policy Solution:**

* Deny images using `:latest`

---

### 4.4 Restricting Image Registries (Supply Chain Security)

**Problem:**

* Developers pull images from public or untrusted registries

**Validating Policy Solution:**

* Allow images only from approved registries

---

### 4.5 Mandatory Labels & Annotations (Governance)

**Problem:**

* Missing labels break monitoring, billing, and ownership tracking

**Validating Policy Solution:**

* Enforce required labels like `app`, `team`, `environment`

---

## 5. Enforce Mode vs Audit Mode (Very Important)

### Enforce Mode

* Policy violations **block the request**
* Used in **strict production environments**

### Audit Mode

* Violations are logged but **request is allowed**
* Used when:

  * Introducing new policies
  * Testing impact safely

### Production Best Practice

1. Start with **Audit mode**
2. Observe violations
3. Fix workloads
4. Switch to **Enforce mode**

---

## 6. Advantages of Validating Policies

### 6.1 Strong Security Guardrails

* Prevent insecure workloads
* Reduce attack vectors

### 6.2 Cost Optimization

* Enforces resource limits
* Prevents runaway workloads

### 6.3 Zero Manual Review

* No need for YAML reviews
* Policies automatically enforce standards

### 6.4 Consistent Cluster Standards

* Same rules for all teams
* No special permissions needed

### 6.5 GitOps Friendly

* Policies stored as code
* Version controlled and auditable

---

## 7. Validating Policy vs Mutating Policy

| Feature           | Validating Policy | Mutating Policy    |
| ----------------- | ----------------- | ------------------ |
| Purpose           | Allow / Deny      | Auto-fix resources |
| Modifies resource | ❌ No              | ✅ Yes              |
| Used for security | ✅ Very high       | Medium             |
| Production usage  | 🔥 Very common    | Common             |

---

## 8. When NOT to Use Validating Policies

Avoid validating policies when:

* Auto-fix is preferred (use mutate policy)
* Developer flexibility is required
* You want default values instead of strict blocking

---

## 9. Real Production Example (Scenario)

**Scenario:**

* Platform team manages EKS cluster
* Developers deploy via ArgoCD

**Validating Policies Enforced:**

* No privileged containers
* Mandatory resource limits
* Approved registries only

**Outcome:**

* Secure, stable, and compliant cluster
* Developers focus only on application code

---


