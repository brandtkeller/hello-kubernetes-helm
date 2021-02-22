# ConfigMap - Basics

Let's configure our configmap (Deployment Pre-requisite).

* Note:
The `show` drop down markers are how to accomplish the task located above it.
Attempt to solve the logic yourself before peeking at the solution. 

We will be targeting a deployment with a hardened nginx container.
It will consume this configmap for purposes of customization and serving out html content.

## Scenario
You are being asked to convert the static manifest for the configmap that the deployment will consume and serve to our users.

- It will be named in a traceable manner.
- Templated to consume a few dynamic variables from the values file.

## Setup
As we will be deploying/tearing down resource as we iterate, let's create a namespace with `kubectl` ahead of time. Name it something unique as to not step on any other operators workloads.

<details><summary>show</summary>
<p>

```bash
kubectl create namespace mynamespace
```

</p>
</details>

## Tutorial Start
The [/k8s/configmap.yaml](../k8s/configmap.yaml) has been configured for a generic/static declaration of the webapp html content. Let's grab this and place it in the empty [/templates/configmap.yaml/](../templates/configmap.yaml).

Now to get started - The ConfigMap is relatively straight forward. Let's re-iterate a few terms for ease of understanding.
* `release name`
    * This is the name given to a specific deployment of a chart
    * IE: `helm install testing123 ../hello-kubernetes/ -n mynamespace` - `testing123` is my release name.
* `chart name`
    * This is the name given in the [Chart.yaml](../Chart.yaml) and is a more static reference to taht actual application being deployed.
* `fullname`
    * This is a combination of your release name `testing123` + the chart name `hello-kubernetes` = `testing123-hello-kubernetes`

The `fullname` is a way to reference a specific deployment of an application while also signifying what application the release belongs to.

Let's Begin by adding the `fullname` reference to the ConfigMap name. This can be included by using `{{ include "hello-kubernetes.fullname" . }}`

<details><summary>show</summary>
<p>

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "hello-kubernetes.fullname" . }}
data:
  index.html: |
    <h1>Hello Kubernetes Helm Tutorial</h1>
    <h2>Author: "<yournamehere>"</h2>
    <p>If you can see this page, that means the content has been mounted to the pod through this configmap correctly.<p>
```

</p>
</details>

Now let's add some customization. The author for the deployment of the chart can claim the name of the resource served to clients.
Let's first add a value to the [values.yaml](../values.yaml) for author.

<details><summary>show</summary>
<p>

```
author: "brandt keller"
```

</p>
</details>

Then, let's template this variable into our ConfigMap. Variables from the `values.yaml` file can be referenced through use of `{{ .Values.variablename }}`. Replace the `<yournamehere>` portion of the html in the ConfigMap with the value from the `values.yaml`.

<details><summary>show</summary>
<p>

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "hello-kubernetes.fullname" . }}
data:
  index.html: |
    <h1>Hello Kubernetes Helm Tutorial</h1>
    <h2>Author: "{{ .Values.author }}"</h2>
    <p>If you can see this page, that means the content has been mounted to the pod through this configmap correctly.<p>
```

</p>
</details>

With those established - We have no concluded the setup of our ConfigMap with both a reference to the charts `fullname` and variable templating from the `values.yaml` file. Congrats! Now let's move on to the deployment.

## Next Steps
Please continue on to the next section [01_deployment.md](../instructions/01_deployment.md) where we will begin the migration of our deployment resource.