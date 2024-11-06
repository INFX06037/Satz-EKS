
STEPS: 
----------------
### Creating Kubernetes Cluster using EKSCTL:
Open powershell as administrator, run the following cmds  

**Install choco manager in powershell**
- Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

**Install eksctl & kubectl in powershell**
- choco install eksctl <<(Most Preferred way in organizations)
- choco install kubernetes-cli
- eksctl version
- Kubectl version
  
**Install AWS-CLI**
- choco install awscli --force
- aws configure  
  Enter the access keys <<(Note: the access keys and secret keys must have full permission)

**Install GIT**
- choco install git
- git clone https://github.com/INFX06037/Satz-EKS  
 *This will download all the yaml files from git*

**Create EKS Cluster**
- eksctl create cluster -n “digital-cluster" -r "ap-south-1" --version "1.30" --nodegroup-name “digital-nodegrp" -t "t3.micro" -N 2

Note: It takes max 15-30 mins to create a cluster


------------------
POD
-------------------
- kubectl get pods   
- kubectl apply -f pod.yaml
- kubectl get pods
- kubectl delete -f pod.yaml

------------------
NODES
-------------------
- kubectl get nodes

------------------
NAMESPACE
-------------------
- kubectl get ns
- kubectl apply -f .\namespace.yaml
- kubectl get ns  
 *Lets work with pod in digital namespace*  
- kubectl get pods
- kubectl apply -f .\podwithns.yaml
- kubectl get pods -n digital
- kubectl exec -it nginx-pod-2 -n digital -- /bin/bash
- apt-get update -y
- apt install vim -y
- cd /usr/share/nginx/html
- vi index.html
- service nginx status  
 *set default namespace to digital*  
- kubectl config set-context --current --namespace digital
- exit

------------------
REPLICASET
-------------------
**It is used to autoscale the pods**  
- kubectl apply -f replicaset.yaml
- kubectl get replicasets -n digital (or) kubectl get rs
- kubectl get replicasets --all-namespaces
- kubectl describe replicasets nginx-replicaset -n digital (or) kubectl describe rs
- kubectl edit replicasets digital-replicaset
- kubectl delete replicasets digital-replicaset


------------------
DEPLOYMENTS
-------------------
**It is used to manage and deploy the applications in the pod**  
- kubectl get deployments
- kubectl apply -f .\deployment.yaml
- kubectl get deployments --all-namespaces
- kubectl describe deployment
- kubectl scale deployment nginx-deployment --replicas=5

**Rolling Update (Deployment Updates)**
- kubectl set image deployment/nginx-deployment nginx-container=nginx:1.27.0
- kubectl rollout status deployment/nginx-deployment
  *What if, deployment got failed due to application issue*
- kubectl rollout undo deployment/nginx-deployment
- kubectl delete deployment nginx-deployment -n digital


------------------
SERVICE
-------------------
**It is used to expose the pod with the public internet**  
**3 types of service are availabe in K8S**
1. Cluster IP    =  exposes with in the cluster
2. Nodeport      =  exposes with in your subnet (vpc network)
3. Loadbalancer  =  exposes to the outside world
   
- kubectl get svc
- kubectl apply -f .\nodeport-service.yaml
- kubectl get svc
- kubectl apply -f .\loadbalancer-service.yaml
- kubectl get svc
*Now check the loadbalancer DNS name in browser to see the output*


------------------
LOGS & EVENTS
-------------------
- kubectl logs nginx-deployment-84b5985966-m6bcn
- kubectl describe pod nginx-deployment-84b5985966-m6bcn

**How to handle memory issue in pod**  
1. Increase the memory limit of the pod, by editing the podwithns.yaml
2. If no memory available, change the instance type of the nodes  
- kubectl get nodes
1. create a secondary nodegroup with t3 medium and delete the default node group
2. Now pods running in the default node group will be migtrated to secondary node group
3. All the pods will be running in the seconday node group successfully
- kubectl get pods

---
