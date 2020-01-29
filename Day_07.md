# Day 7

## Exercise Cluster Upgrade

Tips: 
* Install new Cluster **versión 1.16**: More Info [Day_02](Day_02.md)
* [Execute kubeadm Upgrade Process](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

## Scheduler Pods, Pods Priorities And QoS

* https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/
* https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/


## [Securing a cluster](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/)

* Use Transport Layer Security (TLS) for all API traffic
* Authentication
* Authorization
* [Controlling access to the Kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-authentication-authorization/)
* Controlling the capabilities of a workload or user at runtime
* Protecting cluster components from compromise

### Use Transport Layer Security (TLS) for all API traffic

* [certs management](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/)

### [Authentication](https://kubernetes.io/docs/reference/access-authn-authz/authentication/)

* [Manage Service Accounts](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/)

### [Authorization](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)

Kubernetes reviews only the following API request attributes:

* user - The user string provided during authentication.
* group - The list of group names to which the authenticated user belongs.
* extra - A map of arbitrary string keys to string values, provided by the authentication layer.
* API - Indicates whether the request is for an API resource.
* Request path - Path to miscellaneous non-resource endpoints like /api or /healthz.
* API request verb - API verbs like get, list, create, update, patch, watch, delete, and deletecollection * are used for resource requests. To determine the request verb for a resource API endpoint, see Determine the request verb.
* HTTP request verb - Lowercased HTTP methods like get, post, put, and delete are used for non-resource requests.
* Resource - The ID or name of the resource that is being accessed (for resource requests only) – For resource requests using get, update, patch, and delete verbs, you must provide the resource name.
* Subresource - The subresource that is being accessed (for resource requests only).
* Namespace - The namespace of the object that is being accessed (for namespaced resource requests only).
* API group - The API Group being accessed (for resource requests only). An empty string designates the core API group.

#### Mode

The Kubernetes API server may authorize a request using one of several authorization modes:

* [Node](https://kubernetes.io/docs/reference/access-authn-authz/node/)
* [ABAC](https://kubernetes.io/docs/reference/access-authn-authz/abac/)
* [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#privilege-escalation-prevention-and-bootstrapping)
* [Webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/)
* AlwaysDeny
* AlwaysAllow

#### Admision Control

```
NamespaceLifecycle, LimitRanger, ServiceAccount, TaintNodesByCondition, Priority, DefaultTolerationSeconds, DefaultStorageClass, StorageObjectInUseProtection, PersistentVolumeClaimResize, MutatingAdmissionWebhook, ValidatingAdmissionWebhook, RuntimeClass, ResourceQuota
```

* https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
* https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/

### Controlling the capabilities of a workload or user at runtime

* Limiting resource usage on a cluster
* Controlling what privileges containers run with
* Preventing containers from loading unwanted kernel modules
* Restricting network access
* Restricting cloud metadata API access
* Controlling which nodes pods may access 

### Protecting cluster components from compromise

* Restrict access to etcd
* Enable audit logging
* [Restrict access to alpha or beta features](https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/)
* Rotate infrastructure credentials frequently
* Review third party integrations before enabling them
* [Encrypt secrets at rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)
* Receiving alerts for security updates and reporting vulnerabilities
    https://groups.google.com/forum/#!forum/kubernetes-announce
    https://kubernetes.io/security/

## Pod Security
https://kubernetes.io/docs/concepts/policy/pod-security-policy/
https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
