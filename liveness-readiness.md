# Kubernetes Liveness & Readiness Probes

## Liveness Probes

With the application previously started hit the endpoint `/health` of `sa-web-app` service.

```
curl http://${SA_WEB_APP_IP}/health
```
Now let's simulate that the application gets into a corrupted state:

```
curl http://${SA_WEB_APP_IP}/destroy
```

The call to the endpoint `destroy` turns on a flag to throw exceptions on every subsequent call. Let's verify this:

```
curl http://${SA_WEB_APP_IP}/health
```

An internal server error is returned. That's a sign that the application is **not healthy**, but Kubernetes doesn't restart it. And that's because for Kubernetes there are no health problems. We need to inform Kubernetes how to do health checks. That's what Liveness Probes do.

### Liveness Probes Introduction

Liveness probes are defined in the container definition in the PodSpec as shown below:

```yaml
        livenessProbe:
          httpGet:                # 1
            path: /health         # 2
            port: 8080
```

* **httpGet** defines that we are making an http request. Other options are **exec** and **tcp**
* **path** defines the path to be called.

Let's kill the container again:

```
curl http://${SA_WEB_APP_IP}/destroy
```

Verify that the container restarts by observing the output for approx. 40 seconds:

```
$ kubectl get pods -w
```

Kubernetes now is informed when an application is not healthy, and performs health check in the way we defined them. The health check is internally done by the kubelet. Kubelet?!

> The kubelet is the agent installed in every node that receives the PodSpec and makes sure to run the containers and keep them healthy (or restart otherwise).

### Health Check Properties

The health check are configurable with different properties, we can investigate the default configuration by executing the command below:

```
$ kubectl describe pod <pod_name> | grep Liveness:
    Liveness:       http-get http://:8080/health delay=0s timeout=1s period=10s #success=1 #failure=3
```

In this output we can see how the liveness probe is performed.

* delay=0 - there is no delay for starting the health checks.
* timeout=1s - after a probe timeout for 1 second.
* period=10s - try every 10 seconds.
* failure=3 - after 3 failures mark the pod as Unhealthy.
* success=1 - Mark the service as Healthy if it passes one successful check.

A sample definition with all of the properties:
```
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 2  # delay proeprty
          failureThreshold: 5     # failure property
          timeoutSeconds: 3       # timeout property
          periodSeconds: 10       # period property
          successThreshold: 1     # success property
```

## Graceful Shutdown Period

In the file `deployment` file, we squeezed another important line that we want to take a closer look at:

```
terminationGracePeriodSeconds: 60
```

This informs the Kubelet to give our application 60 seconds of time to gracefully shutdown, before killing the process.
For applications with state and or long running transactions this is very important.

The termination cycle goes as follows:
```
[Application Unhealthy]

1 2
│ ├── preStop Hook is triggered
│ │
│ ├── SIGTERM signal sent to the pod
│ │
x ├── SIGKILL signal sent to the pod.
```

* The vertical line 1. represents the time of terminationGracePeriodSeconds which is being count from the moment the application is unhealthy. When defining the value for terminationGracePeriodSeconds you need to take into account the period of time it takes for the preStop Hook to complete. If it takes longer than the gracefull shutdown the SIGKILL command will be executed before it going to completion.
* The vertical line 2. represents the lifecycle until we get to SIGKILL. The first two steps should ensure that the pod is gracefully shut down.

Kubernetes doesn't have to wait for the entire termination grace period, if the application terminates earlier then the process is killed.

> The applications need to be updated to properly handle the SIGTERM signal. Going into this is out of the scope of this article.

## Readiness Probes

Liveness Probes ensure that the application is healthy (and restarted when not) meanwhile Readiness Probes ensure when requests can be loadbalanced to the application.

To put it blatantly:

* Liveness Probes optimize the time in which an application is in healthy state.
* Readiness Probes optimize the success rate of requests, by not forwarding traffic when the application is not ready.

The application should not receive requests in the following typical scenarios:

* During starting phase of the application. For an application to be ready to receive requests it can take minutes.
* While the application is not healthy. There is a period of graceful shutdown for the application where it is still Running but it should not receive requests.

Sounds good let's apply the following readiness probe:

```
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          periodSeconds: 5
          failureThreshold: 1
          successThreshold: 1
          timeoutSeconds: 1
```

## Liveness and Readiness probe in Action

Follow the following three steps:

* Open Terminal #1 to verify pod going into NotReady state when the application goes corrupt:

```
$ kubectl get pods -w
```

* Open Terminal #2 and execute a get request for the pods health every 0.5 seconds:

```
$ watch -n .5 curl http://${SA_WEB_APP_IP}/health
```

* Open Terminal #3 and hit the destroy endpoint:

```
curl http://${SA_WEB_APP_IP}/destroy
```

Observations:

* In terminal #2 you will see failures for the service that is unhealthy
* In terminal #1 you will see that the unhealthy service goes into Not Ready state `Ready (0/1)` as the readiness probe has failed.
* In terminal #1 after approx. 30 seconds, the container will restart as the liveness probe has failed and a couple of seconds more and it will switch to ready state.

**Bonus:** In kubernetes you can check the logs of the last pod by using --previous flag. Also `kubectl logs <pod-name> --previous`

## Summary

In this article you learned about Liveness and Readiness probes.

* How they work together to ensure that the application is healthy and
* When an application is Ready and Not Ready.
* How we can configure the kubelet to check for Readiness and Healthiness using the PodSpec.

Now you gotta figure out the best configuration for your app ;)
