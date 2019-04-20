## Setting the application up

Initialize `helm` with your cluster:

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

Get IP of the ingress controller by executing the command below:

```
minikube service list
```

Open the IP on your preferred browser
