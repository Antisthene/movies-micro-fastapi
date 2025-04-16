# README

* Repo: https://github.com/Antisthene/movies-micro-fastapi
* On any push on dev, qa branch, the Jenkinsfile is launched

## DockerHub public links:
* Cast Image: https://hub.docker.com/r/al38984/jenkins-cast-fastapi 
* Movie Image: https://hub.docker.com/r/al38984/jenkins-movie-fastapi 

## Setup
```
kubectl create ns dev
kubectl create ns prod
kubectl create ns staging
kubectl create ns qa
```
### Common
```
export URL=$(curl ipinfo.io/ip)
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
helm list -n dev
```

## HELM
Helm chart creations on dev namespace. Shift dev by prod, qa or staging to have all env.
```
cd k8s
helm install cast-chart ./cast --values=./cast/values.yaml -n dev
helm install movie-chart ./movie --values=./movie/values.yaml -n dev
helm install nginx-chart ./nginx --values=./nginx/values.yaml -n dev

kubectl get po,svc,deploy,pvc,secret,configmap -n dev
curl http://$URL:30002/api/v1/casts/docs

helm uninstall cast-chart -n dev
helm uninstall movie-chart -n dev
helm uninstall nginx-chart -n dev
```
