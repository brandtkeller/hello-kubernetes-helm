# Deployment - Basics

Let's configure our workload (where all of our heavy lifting is done).

The `show` drop down markers are how to accomplish the task located above it.
Attempt to solve the logic yourself before peeking at the solution. 

We will be targeting a approved/hardnened image from [ironbank](https://registry1.dso.mil/ironbank/).
* `registry1.dso.mil/ironbank/opensource/nginx/nginx:1.19.2`
* You can also target a local version of the ironbank image to remove the compliexity of needing an image pull secret configured for this tutorial.

## Scenario
You are being asked to convert the static manifest for a web application to a helm chart for purpose of portability and extensiblity. 

The deployment consists of:
- A deployment kubernetes resource
- A hardened nginx image that exposes port 8080 for http traffic
- A reference to the ConfigMap that we added to the templates directory in the 

## Tutorial Start
The [/k8s/deployment.yaml](../k8s/deployment.yaml) has been configured for a generic/static declaration of the webapp. Let's grab this and place it in the empty [/templates/deployment.yaml/]()

Now that this can been migrated - We can begin templating this resource.

First-things-first we will again utilize `fullname` to replace both the name of the deployment and all mentions of `app: hello-kube` labels.

<details><summary>show</summary>
<p>

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: {{ include "hello-kubernetes.fullname" . }}
  name: {{ include "hello-kubernetes.fullname" . }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "hello-kubernetes.fullname" . }}
  strategy: {}
  template:
    metadata:
      labels:
        app: {{ include "hello-kubernetes.fullname" . }}
    spec:
      containers:
      - image: registry.home.local:8443/ironbank/opensource/nginx/nginx:1.19.2
        name: nginx
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: index
          mountPath: /usr/share/nginx/html
      volumes:
      - name: index
        configMap:
          name: hello-config
```

</p>
</details>

We have already configured the ConfigMap to deploy with `name: {{ include "hello-kubernetes.fullname" . }}` metadata. So we can also add this to the name of the ConfigMap mentioned in the volumes block.

<details><summary>show</summary>
<p>

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: {{ include "hello-kubernetes.fullname" . }}
  name: {{ include "hello-kubernetes.fullname" . }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "hello-kubernetes.fullname" . }}
  strategy: {}
  template:
    metadata:
      labels:
        app: {{ include "hello-kubernetes.fullname" . }}
    spec:
      containers:
      - image: registry.home.local:8443/ironbank/opensource/nginx/nginx:1.19.2
        name: nginx
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: index
          mountPath: /usr/share/nginx/html
      volumes:
      - name: index
        configMap:
          name: {{ include "hello-kubernetes.fullname" . }}
```

</p>
</details>

Now comes increasing the functionality/portability of this chart. Let's say for instance that we want to distribute this chart to be used by a bunch of organizations who have the own internal registry with images that have slightly different names. We can create variables for the deployment to consume that will allow overwriting at time of deployment without needing to actually alter the `values.yaml` content (more on this later).

Let's add a block in the `values.yaml` for `image` and create object parameters for this so that we can reference the registry through `image.registry`, the image name through `image.name` and the tag through `image.tag`. Fill those variables in with your registry/image information

<details><summary>show</summary>
<p>

```
image:
  registry: registry.home.local:8443
  name: ironbank/opensource/nginx/nginx
  tag: 1.19.2
```

</p>
</details>

Great, now let's templatize that information into our templates/deployment.yaml through the use of those variables.
Remember the format of `{{ .Values.variablename }}`

<details><summary>show</summary>
<p>

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: {{ include "hello-kubernetes.fullname" . }}
  name: {{ include "hello-kubernetes.fullname" . }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "hello-kubernetes.fullname" . }}
  strategy: {}
  template:
    metadata:
      labels:
        app: {{ include "hello-kubernetes.fullname" . }}
    spec:
      containers:
      - image: "{{ .Values.image.registry }}/{{ .Values.image.name }}:{{ .Values.image.tag }}"
        name: nginx
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: index
          mountPath: /usr/share/nginx/html
      volumes:
      - name: index
        configMap:
          name: {{ include "hello-kubernetes.fullname" . }}
```

</p>
</details>

Next up! port configuration. In the event that someone wants to use a modified image with this chart - we can template these values to be easily interchangable. Additionally, you will see how these values are great non-static references that can be used in multiple templates and even testing!

As we look at the k8s manifests, this port is referenced by both the deployment AND the service. Let's create a service object in the `values.yaml` file and throw a `port` parameter under it w/ the desired default value.

<details><summary>show</summary>
<p>

```
service:
  port: 8080
```

</p>
</details>

AS we've done time and time before, let's make use of this value in our deployment. This will be the end result for the current iteration of the deployment modifications. 

<details><summary>show</summary>
<p>

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: {{ include "hello-kubernetes.fullname" . }}
  name: {{ include "hello-kubernetes.fullname" . }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ include "hello-kubernetes.fullname" . }}
  strategy: {}
  template:
    metadata:
      labels:
        app: {{ include "hello-kubernetes.fullname" . }}
    spec:
      containers:
      - image: "{{ .Values.image.registry }}/{{ .Values.image.name }}:{{ .Values.image.tag }}"
        name: nginx
        ports:
        - containerPort: {{ .Values.service.port }}
        volumeMounts:
        - name: index
          mountPath: /usr/share/nginx/html
      volumes:
      - name: index
        configMap:
          name: {{ include "hello-kubernetes.fullname" . }}
```

</p>
</details>

## Next Steps
Please continue on to the next section [02_service.md](../instructions/02_service.md) where we will begin the migration of our service resource.