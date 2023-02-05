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

1. To get information about the worker node/s we need _kubectl_ commands:\
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
