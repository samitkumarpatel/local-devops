# minikube

### Create a Cluster
```sh

```

### Enable Ingress
```sh
minikube addons enable ingress
```

### Check if the ingress is ready or not
```sh
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s

```

### Example

```sh
kubectl create deployment web --image=gcr.io/google-samples/hello-app:1.0
kubectl expose deployment web --type=NodePort --port=8080
kubectl get service web
minikube service web --url
kubectl apply -f https://k8s.io/examples/service/networking/example-ingress.yaml

kubectl create deployment web2 --image=gcr.io/google-samples/hello-app:2.0
kubectl expose deployment web2 --port=8080 --type=NodePort
```

edit the ingress file and add

```yaml
- path: /v2
  pathType: Prefix
  backend:
    service:
      name: web2
      port:
       number: 8080
```

and apply the ingree. The finale Ingress will look like below

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
  name: example-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: minikube-ingress.net
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web
            port:
              number: 8080
      - path: /v2
        pathType: Prefix
	backend:
          service:
            name: web2
            port:
              number: 8080

```
> make sure in the host name `minikube-ingress.net` mention in the ingress is correct and expected.

### Notes
To create a local dns name , point to your Ingress IP can be done this way:

Add the following line to the bottom of the /etc/hosts file on your computer (you will need administrator access):
172.17.0.15 minikube-ingress.net

The above IP can be found from Ingress `kubectl get ingress example-ingress`

For more details [get help from here](https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/)

