# Certification Tips - Imperative Commands with kubectl
  - Take me to the [Tutorial](https://kodekloud.com/courses/539883/lectures/10503265)

  -
    ![cmds1](../../images/cmds1.png)

  -
    ![cmds2](../../images/cmds2.png)

  -
    ![cmds3](../../images/cmds3.png)

  -
    ![cmds4](../../images/cmds4.png)

  -
    ![cmds5](../../images/cmds5.png)

  -
    ![cmds6](../../images/cmds6.png)

  -
    ![cmds7](../../images/cmds7.png)
    
 -
    ![cmds8](../../images/cmds8.png)




#  Helpfull Commands
https://kubernetes.io/docs/reference/kubectl/conventions/

- POD
	```
  	Create an NGINX Pod

  		kubectl run nginx --image=nginx

  		kubectl run --generator=run-pod/v1 sw --image=nginx

		kubectl run --generator=run-pod/v1 ws --image=nginx --dry-run=client -o yaml > pod.yaml
	```

- Deployment
	```
	kubectl create deployment --image=nginx ws

	kubectl create deployment --image=nginx nginx --dry-run=client -o yaml

	kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > deployment.yaml


	*** kubectl create deployment does not have a --replicas option.
	    You could first create it and then scale it using the kubectl scale command. 

	```

- Service
	```
	kubectl expose pod ws --port=6379 --name redis-service

	** This will automatically use the pod's labels as selectors


	kubectl expose pod ws --port=6379 --name redis-service --dry-run=client -o yaml > service.yaml


	kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml  

	 *** This will not use the pods labels as selectors, instead it will assume selectors as app=redis.
	     You cannot pass in selectors as an option. 
	     So it does not work very well if your pod has a different label set. 
	     So generate the file and modify the selectors before creating the service)




	kubectl expose pod nginx --name ws-service --type=NodePort --target-port=80 --port=5000 --dry-run=client -o yaml > service.yaml

	*** This will automatically use the pod's labels as selectors, but you cannot specify the node port.
	    You have to generate a definition file and then add the node port in manually before creating the service with the pod.


	kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml > service.yaml
	*** This will not use the pods labels as selectors

	
	- Both the above commands have their own challenges.
	  While one of it cannot accept a selector the other cannot accept a node port. 
	  I would recommend going with the `kubectl expose` command. 
	  If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

	```
	

# Anslysis Done for the commands

- kubectl run ws --image=nginx
   ```	
	POD Details:
		Pod name: ws
		Pod Labels: run=ws

		Container Name: ws
   ```	

- kubectl create deployment ws --image=nginx
  ```
	Deployment Name: ws
	Deployment Labels: app=ws
	Selectors: app=ws

	ReplicaSet Name: ws-xxxxx
	ReplicaSet Labels: app=ws
	Selectors: app=ws
	No of Replicas: 1

	Pod Name: ws-xxxxx-yyyyy
	Pod Labels: app=ws

	Container Name: nginx
	Container Image: nginx
   ```



- kubectl create service clusterip NAME [--tcp=<port>:<targetPort>] [--dry-run=server|client|none] [options]
  ```
    Available Commands:
    clusterip    Create a ClusterIP service.
    externalname Create an ExternalName service.
    loadbalancer Create a LoadBalancer service.
    nodeport     Create a NodePort service.
  ```


- kubectl create service clusterip ws --tcp=5000:80
   ```
	Service Name: ws
	Service Labels: app=ws

	Selectors: app=ws
	port: 5000
	targetPort: 80
   ```

- kubectl create service nodeport web --node-port=30005  --tcp=5000:80
  ```
	Service Name: web
	Service Labels: app=web

	Selectors: app=web
	nodePort: 30005
	port: 5000
	targetPort: 80
   ```



- kubectl expose --help
  
   - Looks up a deployment, service, replica set, replication controller or pod by name and uses the selector for that resource as the selector for a new service on the specified port. A deployment or replica set will be exposed as a service only if its selector is convertible to a selector that service supports, i.e. when the selector contains only the matchLabels component. Note that if no port is specified via --port and the exposed resource has multiple ports, all will be re-used by the new service. Also if no labels are specified, the new service will re-use the labels from the resource it exposes.
```
 Possible resources include (case insensitive):

 pod (po), service (svc), replicationcontroller (rc), deployment (deploy), replicaset (rs)

  --type='': Type for this service: ClusterIP, NodePort, LoadBalancer, or ExternalName. Default is 'ClusterIP'.

  kubectl expose rc nginx --port=80 --target-port=8000
  ```

- kubectl expose deploy ws --type=NodePort --port=8080 --target-port=80 
  ```
 	Service Name: ws
	Service Labels: **** Label which is set for deployment

	Selectors: **** Label which is set for deployment
	nodePort: Not Set
	port: 8080
	targetPort: 80
  ```


- kubectl expose deploy ws --type=NodePort --port=8080 --target-port=80  --name=ws-svc
   ```
 	** Service Name: ws-svc 
   ```



- kubectl expose deploy ws --port=80 --target-port=8000   --selector=type=frontend --type=NodePort
  ```
	** selector:
    	type: frontend
  ```

