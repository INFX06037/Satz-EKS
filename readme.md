
STEPS: 
----------------
**Creating Control Plane using EKSCTL:**

**Install choco manager in powershell**
- choco install eksctl <<(Most Preferred way in organizations)
- choco install kubernetes-cli
- eksctl version
- Kubectl version
  
**Install AWS-CLI**
- choco install awscli --force
- aws configure and enter the keys <<(Note: the access keys and secret keys must have full permission)

**Create EKS Cluster**
- eksctl create cluster -n “test-cluster" -r "us-east-1" --version "1.28" --nodegroup-name “demo-nodegrp" -t "t3.micro" -N 2

Note: It takes max 15-30 mins to create a cluster

**Clone the AWS repo**  
- git clone https://github.com/INFX06037/Satz-EKS


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



------------------
SERVICE
-------------------

------------------
REPLICASET
-------------------

------------------
LOGS & EVENTS
-------------------
- kubectl logs nginx-deployment-84b5985966-m6bcn
- kubectl describe pod nginx-deployment-84b5985966-m6bcn
**How to handle memory issue in pod**f
1. Increase the memory limit of the pod
2. If no memory available, change the instance type of the nodes  
- kubectl get nodes
1. create a secondary nodegroup with t3 medium and delete the default node group
2. Now pods running in the default node group will be migtrated to secondary node group
3. All the pods will be running in the seconday node group successfully
- kubectl get pods

---
