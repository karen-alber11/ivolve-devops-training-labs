# Lab 7: Multi-Network Microservices, Volumes, and Bind Mounts

## Sequential Commands & Workflow

```bash
# 1. Clone the application code repository and navigate into it
git clone [https://github.com/Ibrahim-Adel15/MicroserviceApp.git](https://github.com/Ibrahim-Adel15/MicroserviceApp.git)
cd MicroserviceApp

# 2. Build images for frontend, backend, and notification services using their respective source directories as build contexts
docker build -t frontend-image frontend/
docker build -t backend-image backend/
docker build -t notification-image notification/

# 3. Create two bridge networks with separate subnets for service isolation
docker network create --subnet=172.20.1.0/24 frontend-backend
docker network create --subnet=172.20.2.0/24 backend-notification
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab7$ docker network ls
NETWORK ID     NAME                   DRIVER    SCOPE
e996f089f457   backend-notification   bridge    local
e0de766a7be0   bridge                 bridge    local
22aa39ee8a53   frontend-backend       bridge    local
0cda191fef23   host                   host      local
d8ba882f7381   kind                   bridge    local
ccddaed5e47d   none                   null      local


# 4. Create two Docker volumes to persist backend and notification logs
docker volume create backend_logs
docker volume create notification_logs
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab7$ docker volume ls
DRIVER    VOLUME NAME
local     7c7eaec29f79f334f7bac5d52cce15bdac08a5679f3a0e223004ffed03136280
local     backend_logs
local     minikube
local     n8n_data
local     notification_logs


# 5. Create a host directory to persist backend application data via bind mount
mkdir -p /home/nti/backend_data

# 6. Run the notification container on the backend-notification network with persistent logs volume
docker run -d --name notification-service \
  --network backend-notification \
  -v notification_logs:/app/logs \
  notification-image

# 7. Run the backend container on the frontend-backend network with bind mount and logs volume attached
docker run -d --name backend-service \
  --network frontend-backend \
  -v /home/nti/backend_data:/app/data \
  -v backend_logs:/app/logs \
  -e NOTIFICATION_URL=http://notification-service:5001 \
  backend-image

# 8. Connect the backend container to the second network (backend-notification) for multi-network communication
docker network connect backend-notification backend-service

# 9. Run the frontend container on the frontend-backend network, publishing port 8080 and linking the backend URL
docker run -d --name frontend-service \
  --network frontend-backend \
  -p 8080:8080 \
  -e BACKEND_URL=http://backend-service:5000 \
  frontend-image

# 10. Verify application functionality and service communication
curl http://localhost:8080
