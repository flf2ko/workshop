# Chapter 6 - Kubernetes 201 (Reduced version)

---

## Objective

* Manage a Deployment.
* Manage a Service.

---

## Deployment Management

Create an nginx Deployment:

```shell
kubectl create -f https://k8s.io/examples/application/deployment.yaml
```

---

## Check your deployment

List all Deployments:

```shell
kubectl get deployment
```

List the Pods created by the Deployment:

```shell
kubectl get pods -l app=nginx
```

---

## Upgrade your deployment

Upgrade the nginx container from 1.7.9 to 1.8 by changing the Deployment and calling `apply`.  The following config contains the desired changes:

```shell
kubectl apply -f https://k8s.io/examples/application/deployment-update.yaml
```

Watch the Deployment create Pods with new names and delete the old Pods:

```shell
kubectl get pods -l app=nginx
```

---

## Services

Create an nginx Service:

```shell
kubectl create -f https://k8s.io/examples/service/nginx-service.yaml
```

List all services:

```shell
kubectl get services
```

---

### Get the service IP and port

Provided the service IP is accessible, you should be able to access its http endpoint with wget on the exposed port:

```shell
export SERVICE_IP=$(kubectl get service nginx-service -o go-template='{{.spec.clusterIP}}')
export SERVICE_PORT=$(kubectl get service nginx-service -o go-template='{{(index .spec.ports 0).port}}')
```

Check `$SERVICE_IP` and `$SERVICE_PORT`:

```shell
echo "$SERVICE_IP:$SERVICE_PORT"
```

---

## Verify the service

Then, create a busybox Pod:
```shell
kubectl run busybox --generator=run-pod/v1 --image=busybox --restart=Never --tty -i --env "SERVICE_IP=$SERVICE_IP" --env "SERVICE_PORT=$SERVICE_PORT"

u@busybox$ wget -qO- http://$SERVICE_IP:$SERVICE_PORT # Run in the busybox container
u@busybox$ exit # Exit the busybox container
```

After verification, delete the busybox Pod
```shell
kubectl delete pod busybox # Clean up the pod we created with "kubectl run"
```

---

## Delete the nginx Service

To delete the Service by name:

```shell
kubectl delete service nginx-service
```

---

## Delete the nginx Deployment by name:

```shell
kubectl delete deployment nginx-deployment
```
