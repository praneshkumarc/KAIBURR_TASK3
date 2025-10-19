# KAIBURR_TASK3
##FRONTEND - BACKEND CONNECTIVITY GUIDE
##1) Kubernetes (Minikube)
```bash
# Start cluster and use its Docker daemon (run these in your shell)
minikube start
eval $(minikube docker-env)

# Build inside Minikube's Docker (from backend/)
mvn -DskipTests package
docker build -t task-runner-api:podrunner .

# Apply manifests (from backend/, use paths relative to repo root)
kubectl apply -f ../k8s/namespace.yaml
kubectl apply -f ../k8s/rbac.yaml
kubectl apply -f ../k8s/mongo-statefulset.yaml
kubectl apply -f ../k8s/app-deployment.yaml
kubectl apply -f ../k8s/app-service-nodeport.yaml

# Verify resources
kubectl get pods -n task-runner
kubectl get svc task-runner-api -n task-runner

# Call the API via NodePort
HOST=$(minikube ip)
curl -s http://$HOST:30080/api/tasks | python3 -m json.tool

# Create a task and run it
curl -i -X PUT http://$HOST:30080/api/tasks \
  -H 'Content-Type: application/json' \
  -d '{"id":"123","name":"Hello","owner":"You","command":"echo hello from busybox"}'

curl -i -X PUT http://$HOST:30080/api/tasks/123/executions
```

##IF CURL IS WORKING THEN, DO THE FOLLOWING
```bash
#Should run in one terminal
kubectl port-forward svc/mongo -n task-runner 27017:27017

#Should run in another terminal
#inside the backend directory (Download Maven inside 'backend' directory)
export SPRING_DATA_MONGODB_URI="mongodb://localhost:27017/taskrunner"
export RUNNER_MODE="local"
mvn spring-boot:run

#Should run in another terminal
#Inside frontend directory
npm run dev
```

##UI LOOK
<img width="1189" height="806" alt="Screenshot 2025-10-19 221109" src="https://github.com/user-attachments/assets/da2ab2ea-e2d6-43d3-b90b-3bc9976be6db" />

##COMMAND EXECUTION (SINCE WE ARE INSIDE BACKEND DIRECTORY, 'ls' RETURNS FILES INSIDE IT)
<img width="1169" height="799" alt="Screenshot 2025-10-19 221418" src="https://github.com/user-attachments/assets/d63ef4b9-17a8-43d5-9b9a-5f32fe3f0dbf" />

##INSERTING A NEW TASK AND ITS OUTPUT
<img width="1177" height="800" alt="Screenshot 2025-10-19 221741" src="https://github.com/user-attachments/assets/d7b727bf-877a-45c0-bf87-553dba04bae4" />
<img width="1181" height="872" alt="Screenshot 2025-10-19 221824" src="https://github.com/user-attachments/assets/a5216f6a-dfb0-4a30-96a6-ed2a3d9c9118" />

##SEARCH OPERATION
<img width="1183" height="802" alt="Screenshot 2025-10-19 222757" src="https://github.com/user-attachments/assets/c25845d7-150a-417a-8a33-3ec40da7e186" />

##DELETE
<img width="1219" height="456" alt="Screenshot 2025-10-19 224003" src="https://github.com/user-attachments/assets/cfffdabe-fb74-4e23-b635-b137347c32c0" />
<img width="1187" height="815" alt="Screenshot 2025-10-19 224019" src="https://github.com/user-attachments/assets/46baa25e-07a2-4c2b-a7ef-9ed3bb5f8847" />

