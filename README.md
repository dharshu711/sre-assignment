````markdown
# SRE Assignment: Metrics App Deployment

## Overview
In this assignment, I deployed a containerized application that exposes a `/counter` endpoint using Helm, ArgoCD, and KIND. The application increments a counter value each time the `/counter` endpoint is accessed. The steps involved setting up a Kubernetes cluster using KIND, deploying the app with ArgoCD, exposing it using an ingress resource, and validating its behavior.

## Steps and Commands
1. **Create Local KIND Cluster**  
   I created a Kubernetes cluster locally using KIND:  
   ```bash  
   kind create cluster --name sre-assignment  
````

2. **Install ArgoCD on KIND Cluster**
   To install ArgoCD, I applied the ArgoCD installation manifest:

   ```bash
   kubectl create namespace argocd  
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml  
   ```

3. **Expose ArgoCD UI**
   I exposed ArgoCD to access its UI:

   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443  
   ```

4. **Login to ArgoCD**
   I logged into the ArgoCD UI using the default credentials:

   ```bash
   kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-server -o jsonpath='{.items[0].status.containerStatuses[0].state.running.startedAt}'  
   ```

   The default username is `admin`, and the default password is obtained from the ArgoCD secret:

   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d  
   ```

5. **Create Helm Chart for the App**
   I created a Helm chart named `metrics-app` and added the Docker image information (`ghcr.io/cloudraftio/metrics-app:1.1`) along with the required secret for the `PASSWORD` environment variable.

6. **Create ArgoCD Application**
   I created an ArgoCD Application manifest for the app and applied it using kubectl:

   ```bash
   kubectl apply -f metrics-app-application.yaml  
   ```

7. **Install Ingress Controller**
   I installed NGINX ingress controller for exposing the application externally:

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml  
   ```

8. **Expose the App via Ingress**
   I created an ingress resource to expose the app:

   ```bash
   kubectl apply -f metrics-app-ingress.yaml  
   ```

9. **Test the `/counter` Endpoint**
   I tested the `/counter` endpoint by running curl commands to check the counter's value:

   ```bash
   for i in $(seq 1 5)  
   do  
       curl localhost:8080/counter  
   done  
   ```

10. **Validate Deployment**
    I validated the app's deployment by checking the pods, services, and ingress:

```bash
kubectl get pods  
kubectl get svc  
kubectl get ingress  
```

## Conclusion

The deployment was successful, and the `/counter` endpoint incremented its value as expected. The app was exposed externally using an ingress resource and accessed via the ArgoCD UI for deployment management. The assignment was completed by following the steps outlined above.

```
```
