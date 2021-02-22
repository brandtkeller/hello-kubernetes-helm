# Chart deployment - Basics

Congratulations on finishing the basics tutorial and preparing our chart for deployment.

## Deployment

Let's see the result of our labor and attempt to deploy this chart to our cluster using the helm CLI.

### Dry Run
Before we actually run resources against our cluster, we can attempt a `dry run`, which is to test the helm CLI consumption of the chart components and see a list (or errors) of what we would have expected had we executed.

* `helm install hello-test --dry-run --debug ../hello-kubernetes/ -n mynamespace`

If all looks good, you should be presented a number of outputs.
* Information about the release
* Computed Values
* Kubernetes manifests for the static resources after templating

## Real Execution
Now let's actually deploy the chart to our cluster.
* `helm install hello-test ../hello-kubernetes/ -n mynamespace`

Let's check for the resources we expected to deploy and their health with `kubectl`
* `kubectl get configmap,pod,deployment,service -n mynamespace`

```
NAME                                    DATA   AGE
configmap/hello-test-hello-kubernetes   1      69s

NAME                                               READY   STATUS    RESTARTS   AGE
pod/hello-test-hello-kubernetes-64cb57b8cc-s6xbb   1/1     Running   0          <invalid>

NAME                                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-test-hello-kubernetes   1/1     1            1           69s

NAME                                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/hello-test-hello-kubernetes   ClusterIP   10.43.181.136   <none>        8080/TCP   70s
```

You should see something similar!

## Testing our deployed content

### Manual Testing
We can run a temporary busybox image to retrieve content from our application and ensure it communicates/contains what we expect.
* `kubectl run busybox --image=busybox --rm -it -n mynamespace --restart=Never -- wget -O- hello-test-hello-kubernetes:8080/`

We expect the output to look similar to:
```
Connecting to hello-test-hello-kubernetes:8080 (10.43.181.136:8080)
writing to stdout
<h1>Hello Kubernetes Helm Tutorial</h1>
<h2>Author: "brandt keller"</h2>
<p>If you can see this page, that means the content has been mounted to the pod through this configmap correctly.<p>
-                    100% |********************************|   190  0:00:00 ETA
written to stdout
pod "busybox" deleted
```

### Declarative testing
Under [templates/tests/](../templates/tests/) you will see a `test-connection.yaml` resource declaration. It is also templated during chart installation and available to be configured to test our deployed resources. It has been pre-configured to run a quick test to ensure clients receive content when traffic passes through the service.

Execute:
* `helm test hello-test -n mynamespace`

The expected result should look something like:
```
NAME: hello-test
LAST DEPLOYED: Mon Feb 22 04:22:50 2021
NAMESPACE: test
STATUS: deployed
REVISION: 1
TEST SUITE:     hello-test-hello-kubernetes-test-connection
Last Started:   Mon Feb 22 04:24:32 2021
Last Completed: Mon Feb 22 04:24:34 2021
Phase:          Succeeded
NOTES:
1. Congrats, your application has been deployed.
```

## Next Steps
Good work thus far on the migration. Please continue on to the intermediate section [04_deployment.md](../instructions/04_deployment.md) where we will begin to abstract and take our chart deployment to the next level.