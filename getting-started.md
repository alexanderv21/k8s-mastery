## Setting the application up

Initialize `helm` with your cluster.

```
$ helm init
```

Install Ingress controller by executing the command below:

```
$ helm install stable/nginx-ingress --name sa-routing --namespace kube-system --set rbac.create=true
```

From the root directory execute the following command:

```
$ kubectl apply -f ./resource-manifests/
```

Get the `External IP` of the Ingress controller:

```
$ kubectl get svc --all-namespaces -l component=controller
```

Open the external ip in the browser.
