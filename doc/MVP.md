# Install and start minikube

Get the installation instructions for your system at https://minikube.sigs.k8s.io/docs/start/ \
Start minikube with ```minikube start```



# Start minikube dashboard

Open a new terminal window and run ```minikube dashboard```

![Dashboard](./01_k8s.png "Dashboard")



# Create namespace for ArgoCD and install it

```kubectl create namespace argocd```

```kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml```

![ArgoCD](./02_k8s.png "ArgoCD")



# ArgoCD GUI

Configure port forward for ArgoCD GUI \
```kubectl port-forward svc/argocd-server -n argocd 8080:443&```

Open ```localhost:8080``` in your browser. \
Accept the self-signed certificate.

![ArgoCD GUI](./03_k8s.png "ArgoCD GUI")

User name - **admin** \
Password - **get it from secrets, see screenshot**

![password](./04_k8s.png "password")



# Create app
- you should have a git repo with the app. My app repo - https://github.com/alexanderlukjanenko/go-demo-app
- AgroCD GUI press **+ NEW APP** button

## configure general section
- Application name: demo
- Default project
- Sync policy: Manual
- Sync options: Auto-create namespace 

![05](./05_k8s.png "05")

## configure source and destination sections
- Repo URL
- Path: helm
- Cluster URL: select from dropdown
- Set app's namespace. demo in my case 

![06](./06_k8s.png "06")

## press on Create button



# Sync the app and wait while k8s start pods and other resources

![07](./07_k8s.png "07")



# App is up and running

Check the app status, it should be synced, all pods up and running

![08](./08_k8s.png "08")



# Modify configuration at git repo
- let's change the 
```yaml
api-gateway: 
    ...
    service: 
        type: LoadBalancer
``` 

from ```LoadBalancer``` to ```NodePort``` @ ```helm/values.yaml``` file

- modify the file and commit changes to the git repo

- refresh app at ArgoCD GUI, it should show the ```Out of sync``` status

![06](./09_k8s.png "06")

- press **Sync** button and wait for ArgoCD to apply changes

- verify the applied changes

![06](./10_k8s.png "06")



# Verify the app
- configure port forward \
```kubectl port-forward -n demo svc/ambassador 8081:80```
- load the \
```localhost:8081```

![06](./11_k8s.png "06")



# Test the image to ascii graphic API
- Prepare the image, for example the logo.png file
- upload it to the ```img``` app endpoint \
```curl -F 'image=@logo.png' localhost:8081/img/```


![06](./12_k8s.png "06")