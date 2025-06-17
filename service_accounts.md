An acoount used by an application to interact with k8 cluster is a SA.
Ex: Jenkins uses SAs to deploy an application on k8 cluster. 

External Apps uses TOKENS while authenticating kubernetes-API.
**************************************************************************************
Resource requests:
Kubernetes pod_definition with resource requests and limits:

```YAML

apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        memory: "128Mi"
        cpu: "250m"
      limits:
        memory: "256Mi"
        cpu: "500m"
```

requests: Minimum guaranteed resources (used by scheduler).
limits: Maximum allowed resources (enforced at runtime).

Note: A pod cannot use more CPU than mentioned (Throttle) but it can use more memory than mentioned in limits. It is where the pod gets terminated with OOM killed (Out Of Memory).

But Now, what if the developer forgets to mention these requests and limits, it is where the concept of LimitRange comes into picture.

 
```YAML

###LimitRange.yaml

apiVersion: v1
kind: LimitRange
metadata:
  name: mem-cpu-limit-range
spec:
  limits:
  - type: Container
    default:
      cpu: 500m
      memory: 256Mi
    defaultRequest:
      cpu: 250m
      memory: 128Mi
    max:
      cpu: 1
      memory: 512Mi
    min:
      cpu: 100m
      memory: 64Mi
```


You create a LimitRange in a namespace.
When a pod is created in that namespace:
> If no requests/limits are defined, defaults are applied.
> If the pod requests more than the max allowed, it’s rejected.


requests and limits are pod-level but LimitRange is Namespace level.


