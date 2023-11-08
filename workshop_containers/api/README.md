1: Start Docker

2: Start MiniKube `minikube start`

3: Point your Docker Deamon to the Minikube instance `eval $(minikube docker-env)`

4: Build the Docker-image `docker build -t esde_api .`

5: Create Pod `kubectl create -f ./esde-api.yaml`

6: Check if Pod is running `kubectl get pods`

7: Port-forward this Pod so you can test it within the browser `kubectl port-forward esde-api 3000:3000`

8: Execute `curl http://127.0.0.1:3000` or visit the browser @ `http://127.0.0.1:3000`


## Adding an service

To see the list of services use the command: ‘kubectl get services’

If there is no deployment file, first create one:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-deployment-esde
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-test-app
  template:
    metadata:
      labels:
        app: my-test-app
    spec:
      containers:
      - name: esde-api
        image: esde_api:latest
        ports:
        - containerPort: 80
```

Save the above YAML as my-deployment.yaml, and then apply it:
`kubectl apply -f my-deployment.yaml`

Use the get deployments command to show all the deployments:
`kubectl get deployments`

Next step will be exposing a service. To expose an service use the following command:
`kubectl expose deployment my-deployment --type=NodePort --name=my-service`

OPTIONAL:
`kubectl expose deployment test-esde --port=80 --type=NodePort --name=my-service`

As the command shown above you can chance the --type flag to any of the following service types: `ClusterIP`, `NodePort`, `LoadBalancer` or `ExternalName`
Same goes for the following flag: `--name` here you can specify any name you want for the service.
The `expose deployment “name deployment”` has to be the same as your deployment YAML file name. 

When the service is exposed depending on the type of the service.
You can access it. By accessing it you use the following command:
`kubectl get service “name of the service”`

If you want to access the ip of Minikube you need to use the `minikube ip`  command. 
The use this in the URL `http://MINIKUBE_IP:NODE_PORT` to access the service
NODE_PORT is between 30000 - 32000
