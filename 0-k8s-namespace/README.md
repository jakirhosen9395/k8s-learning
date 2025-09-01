# Kubernetes Namespace and kubectx + kubens

## 📌 Table of Contents
1. [Kubernetes Namespace](#kubernetes-namespace)
   - [Namespaces benefits](#namespaces-benefits)
   - [Examples](#examples)
2. [Working with Namespaces](#working-with-namespaces)
3. [kubectx tools](#kubectx-tools)
4. [kubens tools](#kubens-tools)
5. [Hands-on Lab](#hands-on-lab)
6. [Conclusion](#conclusion)

---

## Kubernetes Namespace

Kubernetes-এ **Namespace** হলো cluster এর ভেতরে resource group isolate করার একটা পদ্ধতি।  
একটা cluster এর ভেতরে একাধিক namespace থাকতে পারে এবং প্রতিটি namespace এর ভেতরে resource (Pods, Services, PVCs ইত্যাদি) আলাদা থাকে।

👉 একই namespace এর ভেতরে resource নাম unique হতে হবে, কিন্তু আলাদা namespace এ একই নাম থাকতে পারে।

**Example Use Case:**  
একটি cluster অনেকগুলো team ব্যবহার করছে।  
- Team A → `development` namespace  
- Team B → `staging` namespace  
- Team C → `production` namespace  

এতে team গুলো আলাদা ভাবে তাদের workload deploy করতে পারবে, একে অপরকে affect না করেই।

---

## Namespaces Benefits

1. **Resource isolation**  
   প্রতিটি namespace এর resources (Pods, Services, PVC ইত্যাদি) অন্য namespace থেকে আলাদা থাকে।

2. **Access control (RBAC)**  
   RBAC ব্যবহার করে namespace ভিত্তিক user/team permission দেয়া যায়।

3. **Resource quotas**  
   প্রতিটি namespace এ resource quota define করে দেয়া যায়, যেমন CPU/Memory limit।

4. **Environment segregation**  
   Development, Staging, Production এর মতো আলাদা environment আলাদা namespace এ রাখা যায়।

---

## Examples

**Namespace তৈরি (YAML দিয়ে):**
```yaml
#namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev-ns-0
```
```bash
kubectl apply -f namespace.yaml
```

**সব Kubernetes API resources list করো:**
```bash
kubectl api-resources
kubectl api-resources -o wide
kubectl api-resources --namespaced=true
kubectl api-resources --namespaced=false
```

---

## Working with Namespaces

**কতগুলো Namespace আছে?**
```bash
kubectl get namespace --no-headers | wc -l
```

**Namespace তৈরি:**
```bash
kubectl create ns dev-ns-1
```

**নির্দিষ্ট namespace এ Pod count:**
```bash
kubectl get pods --namespace=dev-ns-1
```

**Namespace switch করা (kubectl context):**
```bash
kubectl config set-context --current --namespace=dev-ns-1
```

**Namespace এ Deployment তৈরি:**
```bash
kubectl create deployment nginx-deploy --image=nginx --replicas=2 -n=dev-ns-1
```

**নির্দিষ্ট namespace এ pod list (details সহ):**
```bash
kubectl get pods -o wide -n dev-ns-1
```

**সব namespace এর সব pod list:**
```bash
kubectl get pods -o wide --all-namespaces
```

---

## kubectx Tools

**kubectx** হলো cluster context switch করার টুল। kubeconfig-এ একাধিক cluster থাকলে সহজে switch করা যায়।

### Install (Ubuntu Snap)
```bash
sudo snap install kubectx --classic
```

### Examples
```bash
kubectx                # সব context দেখাবে
kubectx minikube       # minikube context এ switch
kubectx -              # আগের cluster এ ফেরা
kubectx dublin=gke_ahmetb_europe-west1-b_dublin  # context rename
```

---

## kubens Tools

**kubens** হলো namespace switch করার টুল। kubectl config set-context manual না করে সহজে namespace change করা যায়।

### Examples
```bash
kubens kube-system    # kube-system namespace active
kubens default        # default namespace এ ফেরা
kubens -              # আগের namespace এ ফেরা
```

---

## Hands-on Lab

### Lab 1: Namespace তৈরি
```bash
kubectl create ns lab-ns
kubens lab-ns
```

### Lab 2: Deployment তৈরি ওই namespace এ
```bash
kubectl create deployment nginx-deploy --image=nginx --replicas=2 -n=lab-ns
kubectl get pods -n lab-ns -o wide
```

### Lab 3: Namespace Switch
```bash
kubens default
kubectl get pods
```

---

## Conclusion

- Namespace cluster এর ভেতরে isolation ও resource management দেয়।  
- kubectx cluster context switch সহজ করে।  
- kubens namespace switch সহজ করে।  

👉 Kubernetes operator দের জন্য kubectx + kubens combo productivity এবং troubleshooting অনেক বাড়িয়ে দেয়।
