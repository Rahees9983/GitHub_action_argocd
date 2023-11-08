# GitHub_action_argocd

## Repositories and Tools used

https://github.com/Rahees9983/learn_k8s_with_rahees.git \
Tools used 
  - ArgoCD 
  - GitHub Actions 
  - Kubernetes 
  - Docker 
  - GKE (GCP) 
  - GCP Firewall rules 

## Available Scripts

In this repository I have source code for Calculator App written in NiodeJS. \
I will update the calculators Buttons color only just to execute GitHub Actions pipeline. \
Files you can update to make new changes to the calculator app are below:- \
1. app/public/index.html
2. app/src/component/Button.css

Once you made the changes in above files you can follow below steps:- 
1. Open your terminal and clone this repository itself
   ```bash
   git clone https://github.com/Rahees9983/GitHub_action_argocd.git
   ```
2. edit either of the above files
 ```bash
   vi app/public/index.html
   vi app/src/component/Button.css
   ```
3. Push the changes to the git repository
  ```bash
   git add .
   git commit -m " commit message"
   git tag v1.0.0
   git push origin v1.0.0
   ```
  __NOTE:-__ \
  Make sure your tag start with __v__
  because we have mentioned in the __.github/workflows/create-image.yml__ file that whenever there is a tag pushed that starts with __v*__ pipeline will be triggered

### ArgoCD deployment on the Kubernetes Cluster
1. Deploy ArgoCD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
2. To access the ArgoCD console using ClusterIP
  - By default argocd-server service is of type ClusterIP
  - If you don't want to change servie type and still want to access ArgoCD console execute below command
    ```bash
    kubectl port-forward svc/argocd-server --address=0.0.0.0 -n argocd 8080:443
    ```
  __Note:-__
    
 If you use below command to access the console then at GCP it wan't work because by default is forwards the request at localhost
 ```bash
 kubectl port-forward svc/argocd-server -n argocd 8080:443
 ```

  If you are using GKE cluster for ArgoCD deployment the to access ArgoCD console create a ingress firewall rule like below one
    
   <img width="648" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/cbeebcdd-a3a9-46ed-b154-d10a82377546">
   
   And if you are executing __kubectl port-forward__ command from a GCP instance then make sure your instance have __Cloud API access scopes -->>
Allow full access to all Cloud APIs__

<img width="908" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/e902fb2a-a451-4808-9aa1-5c11ebc0b888">


By default your instance don't have access for GKE API. If this is the case then stop your instance and update it to __Allow full access to all Cloud APIs__


3.  To access the ArgoCD console using NodePort execute the below command to Change the argocd-server service type to NodePort:
    ```bash
    kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
    ```
4. To get the default __password__ for the __ArgoCD console__ execute the below command
   ```bash
   kubectl -n argocd get secrets argocd-initial-admin-secret -o yaml
   ```
   - Then copy the base64 enconded password and the decode it using below command
   ```bash
   echo "SzRHZE9uanlBRGQ5ZzlzTA==" | base64 -d
   ```
    eg. in the below image
   
    <img width="877" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/787cfd3c-f809-45ce-8807-8798a44c21ea">

5. If your ArgoCD is deployed at GKE then create a Ingress firewall rule to allow __NodePort__ value like below one

   <img width="559" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/1b857e50-a75b-45bc-83d9-31d645b33942">

   
6. Hit below URL in your browser
   ```bash
   https://<GCE_INSTANCE_PUBLIC_IP:argocd-server_service_NodePort_value>
   ```
   Using https (443) port
   eg. https://34.67.25.43:30637
   Using http (port 80)
   http://34.67.25.43:32365

   <img width="783" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/6b91d1e4-8ac4-46e5-b3d9-73409a0a0bd4">

   GKE Nodes IP address
   
   <img width="1287" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/d4e8ec86-1051-4cb4-bce6-3aeaced7bc42">

   argocd-server service NodePort value   

   <img width="868" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/a04173fc-718e-4395-a282-188493efe611">
     
7. Default user name for ArgoCD GUI is __admin__

   
### Application deployment on the Kubernetes cluster (ArgoCD)
1. Add REPOSITORY \
a.
  - Go to Settings section and click on the REPOSITORY sections
  - select Via HTTPS
  - select default Project
  - Add Repository URL as:- https://github.com/Rahees9983/learn_k8s_with_rahees \
b. Deployment is of two types manual and automatic (default) \
c. For automatic deployment you have to add a webhook inside the GitHub repo Settings section to follow along follow below steps:- \
    - Go to Settings section
    - Select Webhook and click __Add webhook__
    - Inside __Payload URL__ pass the URL of argocd-server service (it is avialable inside argocd namespace)
    - eg. https://34.67.25.43:30637/api/webhook where, 34.67.25.43 (k8s node IP address), (30637 --> NodePort service port value) and __/api/webhook__ you have to append
      I have exposed the __argocd-server service__ as a NodePort service by default it is cluster IP to change it to NodePort execute the below command
      ```bash
      kubectl -n argocd edit svc argocd-server
       ```
      and change the service type to NodePort 
    - For __Content type__ selcet __application/json__
    - For __Which events would you like to trigger this webhook?__ select __Just the push event__
   Look at the below screenshot

<img width="1334" alt="image" src="https://github.com/Rahees9983/GitHub_action_argocd/assets/38326225/e6f4f50a-aeb2-4877-8738-62557e4fb844">

### `npm start`
  
Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.\
WE may also see any lint Ooom in the Akki Don.

### `npm test`


