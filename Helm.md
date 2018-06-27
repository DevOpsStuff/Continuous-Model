# Helm

Helm is an open-source packaging tool that helps you install and manage the lifecycle of Kubernetes applications. Similar to Linux package managers such as Apt-get and Yum, Helm is used to manage Kubernetes charts, which are packages of pre-configured Kubernetes resources.

Helm has two components

* helm client CLI. Used to create, fetch, search and validate charts and to instruct tiller.
* tiller server. Runs inside the Kubernetes cluster and manages the releases.

# Helm Installation

## Prerequisites:
 * GKE kubernetes Cluster
 * Cloud Sql instance
 * And all the service account Keys
 
First Create a Kubernetes Cluster on gcloud . And run the following commands to install helm 

```
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

helm init uses the `~/.kube/config` configuration file to connect to the Kubernetes cluster. Ensure that your configuration is referencing a cluster that is safe to make deployments.

```
$ kubectl cluster-info
$ kubectl config current-context
```

Use helm client to deploy tiller to the Kubernetes cluster

```
$ helm init --upgrade
```

After installing the helm run the below command to see the `tiller` pod server could be running on the kube-system namespace.

```
$ kubectl get pods --namespace=kube-system
```

# Helm Three Big Concepts

A `Chart` is a Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. Think of it like the Kubernetes equivalent of a Homebrew formula, an Apt dpkg, or a Yum RPM file.


A `Repository` is the place where charts can be collected and shared. It’s like Perl’s CPAN archive or the Fedora Package Database, but for Kubernetes packages.


A `Release` is an instance of a chart running in a Kubernetes cluster. One chart can often be installed many times into the same cluster. And each time it is installed, a new release is created. Consider a MySQL chart. If you want two databases running in your cluster, you can install that chart twice. Each one will have its own release, which will in turn have its own release name.

Helm installs charts into Kubernetes, creating a new release for each installation. And to find new charts, you can search Helm chart repositories.

# Helm Charts
A chart is organized as a collection of files inside of a directory. The directory name is the name of the chart. There are ways to use the Helm charts by using the existing stable helm charts or creating a new charts.

To view all the charts from the repository

```
$ helm repo list
NAME    URL
stable  https://kubernetes-charts.storage.googleapis.com
local   http://127.0.0.1:8879/charts

$ helm search 
```

we can configure helm to use other repository to the current helm repository,

```
$ helm repo add <name> <url>
$ helm repo add helmdemo https://helmcharts.batman.com
```

A basic authentication need to be provided to add the repo to our repository, If needed

```
$ helm repo add helmdemo https://<username>:<password>@helmcharts.batman.com
```

By default, helm search will list only the latest packaged version, to list all the version of a package,

```
$ helm search helmdemo -l
```

Because chart repositories change frequently, at any point you can make sure your Helm client is up to date by running `helm repo update`


# Helm Install Package

To install a new package, use the helm install command. At its simplest, it takes only one argument: The name of the chart.

```
$ helm install stable/<name> (this will fetch from original Helm repo)
$ helm install <Directory path> ( this chart which have created)

$ helm install stable/wordpress
```
Now the `wordpress` chart is installed. Note that installing a chart creates a new release object. The release above is named hakuna-matata. (If you want to use your own release name, simply use the `--name` flag on helm install.)

During installation, the helm client will print useful information about which resources were created, what the state of the release is, and also whether there are additional configuration steps you can or should take.

To keep track of a release’s state, or to re-read configuration information, you can use `helm status`:

```
$ helm status hakuna-matata
```

```
$ helm status sappy-seagull
LAST DEPLOYED: Sat Apr 29 08:38:43 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME                       CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
sappy-seagull-discovery    10.15.242.14   <none>         8761/TCP       1d
sappy-seagull-discovery-1  10.15.245.169  <none>         8761/TCP       1d
sappy-seagull-discovery-2  10.15.252.202  <none>         8761/TCP       1d


NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=sappy-seagull-discovery" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl port-forward $POD_NAME 8080:80
  
```
The above shows the current state of your release.

To list all the deployed releases,

```
$ helm ls
```

Many times, will want to customize the chart to use your preferred configuration. To see what options are configurable on a chart,

```
$ helm inspect values
```

There are two ways to pass configuration data during install:

`--values (or -f)`: Specify a YAML file with overrides. This can be specified multiple times and the rightmost file will take precedence
`--set`: Specify overrides on the command line.
If both are used, --set values are merged into --values with higher precedence.

we can then override any of these settings in a YAML formatted file, and then pass that file during installation.

```
$ helm install -f values.yaml helmdemo/batman 
```

we can specify the namespace and release name too, and keep it in lowercase,

```
$ helm install -f values.yaml helmdemo/batman  --namespace dunkirk --name nolan 
```

Use `--debug` for more detailed to see what is getting installed, And if using `--dry-run` flag to get the manifest 
but it won’t install the chart on the server.

```
$ helm install -f values.yaml helmdemo/batman  --namespace dunkirk --name nolan --debug
```

By default, from the repository only the latest chart version will get installed, to install the particular version,

```
$ helm install -f values.yaml helmdemo/batman --version 2.1.0 --namespace dunkirk --name nolan --debug
```

Use `helm ls` to list deployed releases

```
root@root:~# helm ls
NAME            REVISION        UPDATED                         STATUS          CHART           NAMESPACE
sappy-seagull   4               Sat Apr 29 08:38:43 2017        DEPLOYED        discovery-0.1.1 default
```

# Debugging and Dry-Running

Using templates to generate the manifests require to be able to preview the result. Use `--dry-run --debug` options to print the values and the resulting manifests without deploying the release:

```
  $ helm install ./discovery --dry-run --debug
```

This wont deploy it to the cluster. To debug and deploy use only

```
 $ helm install ./discovery --debug
```

# Helm Upgrading and recovering on Failure

So now we have deployed the application and it is up and running. 

When a new version of a chart is released, or when you want to change the configuration of your release, you can use the `helm upgrade` command.

An upgrade takes an existing release and upgrades it according to the information you provide. Because Kubernetes charts can be large and complex, Helm tries to perform the least invasive upgrade. It will only update things that have changed since the last release.

```
$ helm upgrade <Release Name> <Chart Path>
```

**Working with Revision**

Every deployment in Helm has a Revsion number.

```
$ helm ls
NAME            REVISION        UPDATED                         STATUS          CHART           NAMESPACE
errant-lizzard  1               Sat Apr 29 10:30:08 2017        DEPLOYED        discovery-0.1.1 default
```

Now after making changes in any of the deployment/services.

```
$ helm ls
NAME            REVISION        UPDATED                         STATUS          CHART           NAMESPACE
errant-lizzard  1               Sat Apr 29 10:30:08 2017        DEPLOYED        discovery-0.1.1 default

$helm upgrade errant-lizzard helmdemo/discovery
```

To pass the env variable files, i.e `values.yaml` file

```
$ helm upgrade errant-lizzard helmdemo/discovery -f values.yaml --debug
```

It will upgrade it to new version. And if there is any failure in new version
 
 ```
$ helm ls
NAME            REVISION        UPDATED                         STATUS  CHART           NAMESPACE
errant-lizzard  2               Sat Apr 29 10:37:27 2017        FAILED  discovery-0.1.1 default
```

By seeing the status and Revision coloumn. The deployment is failed and the new revison is changed to 2. To Rollback it to previous deployment.

```
$ helm rollback errant-lizzard 1
Rollback was a success! Happy Helming!

$ helm ls
NAME            REVISION        UPDATED                         STATUS          CHART           NAMESPACE
errant-lizzard  3               Sat Apr 29 10:41:42 2017        DEPLOYED        discovery-0.1.1 default
```

The Revision now changed to 3 

```
$ helm history errant-lizzard
REVISION        UPDATED                         STATUS          CHART           DESCRIPTION
1               Sat Apr 29 10:30:08 2017        SUPERSEDED      discovery-0.1.1 Install complete
2               Sat Apr 29 10:37:27 2017        SUPERSEDED      discovery-0.1.1 Upgrade "errant-lizzard" failed: failed to create
3               Sat Apr 29 10:41:42 2017        DEPLOYED        discovery-0.1.1 Rollback to 1
```

Now Rollback is success and Everything will be running fine back to the older version of the deployment.

# Helm Delete: Deleting a Release

When it is time to uninstall or delete a release from the cluster, use the helm delete command: 

```
$ helm delete hakkuna-matata
```

However, Helm always keeps records of what releases happened. Need to see the deleted releases? `helm list --deleted` shows those, and `helm list --all` shows all of the releases (deleted and currently deployed, as well as releases that failed):

```
$helm ls --all
```

Note that because releases are preserved in this way, you can rollback a deleted resource, and have it re-activate.

# Creating your Own Charts

An to Create a new charts 

```
$helm create <name> # some name
```
the Chart tree will look like below.

```
root@root:~# helm create TeshChart
Creating TeshChart
root@root:~# tree TeshChart/
TeshChart/
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── NOTES.txt
│   └── service.yaml
└── values.yaml

2 directories, 6 files
```

A chart must include a chart definition file, Chart.yaml . Chart definition file must define two properties: `name` and `version`.

# Adding charts to GCS storage

Once if we have one or more charts we can move all the charts to one single Hub(GCS Storage). 

```
$ helm package batman/
$ mkdir batman-charts
$ mv batman-0.1.0.tgz batman-charts/
$ helm repo index batman-charts --url https://<username>:<password>@helmcharts.batman.com
```

## Helm index config(server)

```
helm repo index batmanhelmrepo/ --url https://<username>:<password>@helmcharts.batman.com
#gsutil rsync -d  batmanhelmrepo/ gs://batmanhelmrepo
```
The above will create an index.yaml file in the directory and to move the files to gcs.

Don't use the below command unless you want bucket this to be public.

```
gsutil defacl set public-read gs://batmanhelmrepo
gsutil -h "Cache-Control:private" rsync -d batmanhelm/ gs://batmanhelmrepo
```

** NOTE: Now,go the batmanhelmrepo bucket and uncheck the share public link.**

## Client:
```
helm repo add batman-helm https://<username>:<password>@helmcharts.batman.com
helm repo update
helm repo list
helm search batman-helm
```

To list all the version of batman
```
helm search batman-helm -l
```
To install particular version
```
helm install batman-helm --version <version> --namespace Dunkrik --name Nolan --debug
```


