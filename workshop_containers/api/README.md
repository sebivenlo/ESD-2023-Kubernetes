## Deployment and Self-healing demo

1: Start Docker

2: Start MiniKube `minikube start --driver=docker`

3: Point your Docker Deamon to the Minikube instance.
 - Mac: `eval $(minikube docker-env)`
 - Windows: `minikube docker-env | Invoke-Expression`

4: Build the Docker-image `docker build -t esde_api .`

5: Create Deployment `kubectl create -f ./my-deployment.yaml` (If already exists: `kubectl replace -f ./my-deployment.yaml`)

6: Check if Deployment is running `kubectl get deployments`

7: Port-forward this Deployment so you can test it within the browser `kubectl port-forward deployments/test-deployment-esde 3000:3000`

8: Execute `curl http://127.0.0.1:3000` or visit the browser @ `http://127.0.0.1:3000`

## Autoscaling Demo

9: Use Ctrl + C to break out of the port-forwarding loop

10: Enable the metrics-server by running `minikube addons enable metrics-server`

11: Create Horizontal Pod Autoscaler
`kubectl autoscale deployment test-deployment-esde --cpu-percent=5 --min=1 --max=10`

12: Check if HPA has been created
`kubectl get hpa`

13.1: Again port-forward the Deployment `kubectl port-forward deployments/test-deployment-esde 3000:3000`
13.2: To simulate load and trigger autoscaling, execute a loop of GET requests in a SEPARATE terminal
`while ($true) { curl http://127.0.0.1:3000; }`

14: Wait a few minutes for the metrics-server to collect the needed data, then you can again monitor the HPA with
`kubectl get hpa`

15: You should see the number of pods increase as the CPU load goes over 5%.

16: To stop the load, interrupt the loop in the terminal by pressing Ctrl+C.
