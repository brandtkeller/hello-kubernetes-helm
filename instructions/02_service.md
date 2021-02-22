# Service - Basics

Let's configure our service.

The `show` drop down markers are how to accomplish the task located above it.
Attempt to solve the logic yourself before peeking at the solution. 

## Scenario
You are being asked to convert the static manifest for the servuce that will route traffic to the deployment.

- It will be named in a traceable manner.
- Templated to consume a few dynamic variables from the values file.

## Tutorial Start
The [/k8s/service.yaml](../k8s/service.yaml) has been configured for a generic/static declaration for the webapp. Let's grab this and place it in the empty [/templates/service.yaml/]()

Now that this can been migrated - We can begin templating this resource.

First-things-first we will again utilize `fullname` to replace both the name of the service and all mentions of `app: hello-kube` labels.

<details><summary>show</summary>
<p>

```
apiVersion: v1
kind: Service
metadata:
  name: {{ include "hello-kubernetes.fullname" . }}
spec:
  selector:
    app: {{ include "hello-kubernetes.fullname" . }}
  ports:
    - protocol: TCP
      port: 8080
```

</p>
</details>

We already know that we have a service object with a port parameter in the `values.yaml`, so let's use that to template the `port`. 

<details><summary>show</summary>
<p>

```
apiVersion: v1
kind: Service
metadata:
  name: {{ include "hello-kubernetes.fullname" . }}
spec:
  selector:
    app: {{ include "hello-kubernetes.fullname" . }}
  ports:
    - protocol: TCP
      port: {{ .Values.service.port}}
```

</p>
</details>

aaaaaaaaannndd just like that we are finished with the basic migration of resources to helm, and we can begin deploying to our cluster.
Continue to the next section for preparing and deploying this chart to a kubernetes cluster.

## Next Steps
Please continue on to the next section [03_chart_deployment.md](../instructions/03_chart_deployment.md) where we will check, deploy, and test our chart.