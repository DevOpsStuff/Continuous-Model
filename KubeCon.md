## Kubernetes/Docker/Helm/Jenkins/CI/CD Docs/tuts/Articles

**Awesome kubernetes**

Awesome lists of Docker and kubernetes.

[Kubernetes](https://github.com/ramitsurana/awesome-kubernetes)

[Docker](https://github.com/veggiemonk/awesome-docker)

### Common Kubernetes Deployments Issues

**10 Most Common Reasons Kubernetes Deployment fail**

[Part01](https://kukulinski.com/10-most-common-reasons-kubernetes-deployments-fail-part-1/)

[Part02](https://kukulinski.com/10-most-common-reasons-kubernetes-deployments-fail-part-2/)

[Tips & tricks](https://opsnotice.xyz/kubernetes-tips-tricks/)

## Videos

**KubeCon Videos**

[Year 2017](https://www.youtube.com/playlist?list=PLj6h78yzYM2PAavlbv0iZkod4IVh_iGqV)

**Jenkins Declarative Pipeline**

[Meetup](https://www.youtube.com/watch?v=3R5xh4oeDg0&t=386s)

[PAC](https://www.youtube.com/watch?v=ORNDwYXa4nQ)

[Deep Dive](https://www.youtube.com/watch?v=gnDql_tq2xA)


## kube label commands
```
# Update pod 'foo' with the label 'unhealthy' and the value 'true'.
kubectl label pods foo unhealthy=true

# Update pod 'foo' with the label 'status' and the value 'unhealthy', overwriting any existing value.
kubectl label --overwrite pods foo status=unhealthy

# Update all pods in the namespace
kubectl label pods --all status=unhealthy

# Update a pod identified by the type and name in "pod.json"
kubectl label -f pod.json status=unhealthy

# Update pod 'foo' only if the resource is unchanged from version 1.
kubectl label pods foo status=unhealthy --resource-version=1

# Update pod 'foo' by removing a label named 'bar' if it exists.
# Does not require the --overwrite flag.
kubectl label pods foo bar-
```
