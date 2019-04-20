# sentiment analysis

## Setting the application up on docker

Initialize the application using `docker-compose`

```
$ docker-compose up -d
```

Open the IP on your preferred browser

```
http://localhost
```

## Setting the application up on k8s

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
