1: Start Docker

2: Start MiniKube `minikube start`

3: Point your Docker Deamon to the Minikube instance.
 - Mac: `eval $(minikube docker-env)`
 - Windows: `minikube docker-env | Invoke-Expression`

4: Build the Docker-image `docker build -t esde_api .`

5: Create Deployment `kubectl create -f ./my-deployment.yaml` (If already exists: `kubectl replace -f ./my-deployment.yaml`)

6: Check if Deployment is running `kubectl get deployments`

7: Port-forward this Deployment so you can test it within the browser `kubectl port-forward deployments/test-deployment-esde 3000:3000`

8: Execute `curl http://127.0.0.1:3000` or visit the browser @ `http://127.0.0.1:3000`


