# Helm + jenkins Vs Spinnaker

## Helm + Jenkins

* Multi Branch support
* Rich source of plugins
* Single platform for CI/CD
* ease of creating a namespace,configmap,secrets.
* supports for all Kubernetes cluster version.
* CLI based.
* Need to write our yaml files based on helm template format.
* Single deployment strategies so far I found.

## Spinnaker

* Multi cloud and Multi region support
* Central interface for all Deployments
* More deployment strategies.
* UI Based
* Manunally need to create namespace,configmap,secrets for each cluster/region.
* Faster rollouts and easier rollbacks
* Can bake images, More of like terraform
* Supports for GCP,AWS,and Kubernetes clusters.

