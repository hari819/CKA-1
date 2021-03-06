# Kubernetes Services
  - Take me to [Video Tutorial](https://kodekloud.com/courses/539883/lectures/9808156)
  
In this section we will take a look at **`services`** in kuberentes

## Services
- Kubernetes Services enables communication between various components such as group of POD services within and also allows external outside users to access the application. Thus services enable loose coupling between micro services in our application.

  ![srv1](../../images/srv1.PNG)
  
#### Let's look at some other aspects of networking

## External Communication

- How do we as an **`external user`** access the **`web page`**?

  - From the node (Able to reach the application as expected)
  
    ![srv2](../../images/srv2.PNG)
    
  - From outside world (This should be our expectation, without something in the middle it will not reach the application)
  
    ![srv3](../../images/srv3.PNG)
   
    
 ## Service Types
 
 - Service is an internal virtual entity/component/server inside the node 
 - In the cluster it's has its own virtual IP address and port, so that it can communicate within different groups of pods. That IP address is called as Cluster IP of the Service
 
 #### There are 3 types of service types in kubernetes
 
   ![srv-types](../../images/srv-types.PNG)
 
 1. NodePort
    - Where the services makes an internal POD accessable on a POD on the NODE.
    - Three different types of port are involved in NodePort
     - targetPort: This is the port exposed on the POD/container on which the application is listening
     - port: This is the port on the service, service will listen on Service/Cluster IP and Port
     - NodePort: This is the port opened on the Node so that external user can access the apllication through NodeIP:NodePort, the request will then will routed to the service on ServiceIP:Port and then service will forward the traffic to the application running under the POD
     
    - Mandaory Ports:
      ***Only mandatory paramerer under ports is port parameter. If the targetPort is not specified, it will consider targetPort as port and if the NodePort is not specified, a random port is generated and allocated in range from 30000 - 32676
      
     
      ```
      apiVersion: v1
      kind: Service
      metadata:
       name: myapp-service
      spec:
       types: NodePort
       ports:
       - targetPort: 80
         port: 80
         nodePort: 30008
      ```
     ![srvnp](../../images/srvnp.PNG)
      
      #### To connect the service to the pod
      ```
      apiVersion: v1
      kind: Service
      metadata:
       name: myapp-service
      spec:
       types: NodePort
       ports:
       - targetPort: 80
         port: 80
         nodePort: 30008
       selector:
         app: myapp
         type: front-end
       ```

    ![srvnp1](../../images/srvnp1.PNG)
      
      #### To create the service
      ```
      $ kubectl create -f service-defination.yaml
      ```
      
      #### To list the services
      ```
      $ kubectl get services
      ```
      
      #### To access the application from CLI instead of web browser
      ```
      $ curl http://192.168.1.2:30008
      ```
      
      ![srvnp2](../../images/srvnp2.PNG)

      #### A service with multiple pods
      
       - what do you do when you have multiple PODs? in a production environment. You have multiple instances of your web application running for high availability and load balancing purposes in this case. We have multiple similar pods running the web application they all have the same labels with a key app and set to a value of the app the same label is used as a selector during the creation of the service. So when the service is created it looks for a matching pod with the label and finds three of them. The service then automatically selects all the three pods as endpoints to forward the external requests coming from the user. You don't have to do any additional configuration to make this happen. And if you're wondering what algorithm it uses to balance the load across the three different pods it uses a random algorithm and acts as a built in load balancer to distribute load across
       
      ![srvnp3](../../images/srvnp3.PNG)
      
      #### When Pods are distributed across multiple nodes
      
      - when the pods are distributed across multiple nodes. In this case we have the web application on pods on separate nodes in the cluster. When we create a service without us having to do any additional configuration kubernetes automatically creates a service that spans across all the nodes in the cluster and maps the target port to the same node port on all the nodes in the cluster this way you can access your application using the IP of any node in the cluster and using the same port number which in this case is 30008.
     
      ![srvnp4](../../images/srvnp4.PNG)
     
            
 1. ClusterIP
    - In this case the service creates a **`Virtual IP`** inside the cluster to enable communication between different services such as a set of frontend servers to a set of backend servers.
    
 1. LoadBalancer
    - Where provisions a **`loadbalancer`** for our application in supported cloud providers.
    
K8s Reference Docs:
- https://kubernetes.io/docs/concepts/services-networking/service/
- https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/

