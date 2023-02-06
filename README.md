# k8s-pt1

### ACCEPTANCE CRITERIA

Task list:

  1. __Get information__ about your worker node and __save it in some file__.

  2. __Create a new namespace__ (all resources below will create in this namespace).

  3. __Prepare deployment.yaml file__ which will create a Deployment with 3 pods of Nginx or Apache and service for access to these pods via ClusterIP and NodePort. 
    Show the status of deployment, pods and services. Describe all resources which you will create and logs from pods

  4. __Prepare two job yaml files:__
      - One gets content via curl from an internal port (ClusterIP)
      - Second, get content via curl from an external port (NodePort)

  5. __Prepare Cronjob.yaml file__ which will test the connection to Nginx or Apache service every 3 minutes.

### REALIZATION

__1. To get information about the worker node/s we need _kubectl_ commands:__\
  - To get nodes present in kubecluster:
  ```
  kubectl get nodes 
  ```
  - To get description of the node: 
  ```
  kubectl describe nodes <node>
  ```
  - You can get description for several nodes:
  ```
  kubectl describe nodes <node1> <node2> ...
  ```
  - Its always useful to use built-in options to get result by working with filters:
    >To write the worker __(command section1)__ or the master __(command section2)__ nodes description into the file
  ```
  export NODES=$(kubectl get nodes --selector='!node-role.kubernetes.io/control-plane' -o=custom-columns=NAME:.metadata.name --no-headers)
  kubectl describe nodes $NODES > file.txt
  ```
  ```
  export NODES=$(kubectl get nodes --selector='node-role.kubernetes.io/control-plane' -o=custom-columns=NAME:.metadata.name --no-headers)
  kubectl describe nodes $NODES > file.txt
  ```
  - You can also use single command to do the same thing:
  >To write the worker __(command section1)__ or the master __(command section2)__ nodes description into the file:
  ```
  kubectl describe nodes $(kubectl get nodes --selector='!node-role.kubernetes.io/control-plane' -o=custom-columns=NAME:.metadata.name --no-headers) > file.txt
  ```
  ```
  kubectl describe nodes $(kubectl get nodes --selector='node-role.kubernetes.io/control-plane' -o=custom-columns=NAME:.metadata.name --no-headers) > file.txt
  ```
  In my opinion, using export is better if you're planning to integrate such commands into some loop constructions for some scripts
  
  ![image](https://user-images.githubusercontent.com/109740456/216844927-cfbd1622-7aaf-48fc-adad-a94905e5a960.png)
  
  If you have empty node labels you can also add them by using:
  ```
  kubectl label node <node> <label>.kubernetes.io/<label>=<some-value>
  ```
  For example:
  
  <img src="https://user-images.githubusercontent.com/109740456/216851322-1b14e274-6727-4650-9b3e-055545cd1815.png" width="650">

__2. Creating the namespace:__
```
kubectl create ns <ns-name>
```
<img src="https://user-images.githubusercontent.com/109740456/216851600-cadce98f-c9a0-42c1-b3af-248ee9ce2934.png" width="400">

__3. Preparing and deploying:__

For this task i've created several _.yaml_ files to deploy 3 Nginx replical, 2 types of services, 2 jobs and 1 cronjob.

Its nice to use kubectl command with -n option instead of hardcoding the namespace in .yaml files. In such way its possible to reuse .yaml in different namespaces.

Command to apply:
```
kubectl apply -f <path-to-file-or-dir> -n <namespace>
```
Screenshots:

  - Namespaces:
  ```
  kubectl get namespaces
  ```
  
  <img src="https://user-images.githubusercontent.com/109740456/216852727-b2205a41-bc78-4bc1-b941-e93f3f201530.png" width="460">
  
  - Pods:
  ```
  kubectl get pods -n <ns>
  ```
  
<img src="https://user-images.githubusercontent.com/109740456/216852491-cdf988dc-242f-498c-b934-622af8388654.png" width="600">

  - Deployments
  ```
  kubectl get deployments - n <ns>
  ```
<img src="https://user-images.githubusercontent.com/109740456/216852583-02886cca-7577-4d05-ae12-7d2b1d844373.png" width="550">

  - Services
  ```
  kubectl get svc - n <ns>
  ```
  For creating node-port Service I've opened 30080 tcp port with adding additional rule in TF code:
  
<img src="https://user-images.githubusercontent.com/109740456/216853012-31101852-7eb9-4147-9b14-712b85605de3.png" width="400">
  
<img src="https://user-images.githubusercontent.com/109740456/216852624-891ca3a5-fe82-4075-8532-55d0207d81be.png" width="550">

  - Jobs
  ```
  kubectl get job - n <ns>
  ```
  
<img src="https://user-images.githubusercontent.com/109740456/216852679-07896ce3-e352-4221-b1fb-e8d2af946854.png" width="500">

For getting logs:
```
kubectl logs <obj> -n <ns>
```
  
 Logs for curl-clusterip-job  
    
<img src="https://user-images.githubusercontent.com/109740456/216853317-1dd5a3a5-1c30-43a1-af4e-7242ee15f8c1.png" width="500">
    
 Logs for curl-nodeport-job

<img src="https://user-images.githubusercontent.com/109740456/216853347-a05957c4-1133-4fe3-bc43-e1a2cfe697b9.png" width="500">

 Logs for curl-health-check-cronjob
 
 <img src="https://user-images.githubusercontent.com/109740456/216854024-5a96da2a-15c1-43f4-a16c-6ff0edde5d8c.png" width="600">





