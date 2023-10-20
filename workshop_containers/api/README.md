1: Start Docker
2: Start MiniKube `minikube start`
3: Point your Docker Deamon to the Minikube instance `eval $(minikube docker-env)`
4: Build the Docker-image `docker build -t esde_api .`
5: Create Pod `kubectl create -f ./esde-api.yaml`
6: Check if Pod is running `kubectl get pods`
7: Port-forward this Pod so you can test it within the browser `kubectl port-forward esde-api 5000:5000`
8: Execute `curl http://127.0.0.1:5000` 