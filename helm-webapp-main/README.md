
Project overview:  https://www.youtube.com/watch?v=jUYNS90nq8U&t=47s  
Original project git repo: https://github.com/devopsjourney1/helm-webapp/tree/main  

The project contains:
- "templates-original" folder with original k8s manifest files.
- "solution" folder with the final project from original git
- "webapp1" folder that was used for practice

# Create the helmchart
```
helm create webapp1
```



# Follow along with the video
- Create the files per the video, copying and pasting from templates-original
- you can also use the files in the solution folder

# Install the first one
```
helm install mywebapp-release webapp1/ --values webapp1/values.yaml
```

```shell
 helm list
```

# Get k8s pods/deployment/services

```shell
kubectl get all 
```

Find service `service/mywebapp` and use NodePort to open it in a browser

```shell
curl http://localhost:31667/
```

# Upgrade after templating
```
helm upgrade mywebapp-release webapp1/ --values webapp/values.yaml
```

# Accessing it
```
minikube tunnel
```

# Create dev/prod
```shell
kubectl create namespace dev
kubectl create namespace prod
```

```shell
helm install mywebapp-release-dev webapp1/ --values webapp1/values.yaml -f webapp1/values-dev.yaml -n dev
helm install mywebapp-release-prod webapp1/ --values webapp1/values.yaml -f webapp1/values-prod.yaml -n prod
```

```shell
helm ls --all-namespaces
```

```shell
kubectl get all --all-namespaces
```

```shell
helm uninstall mywebapp-release --namespace default
helm uninstall mywebapp-release-dev --namespace dev
helm uninstall mywebapp-release-prod --namespace prod
```

