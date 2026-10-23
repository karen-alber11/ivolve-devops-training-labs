# Lab 6: Docker Environment Variables and Resource Management

## Sequential Commands & Workflow

```bash
# 1. Clone the application code repository
git clone [https://github.com/Ibrahim-Adel15/PythonApp.git](https://github.com/Ibrahim-Adel15/PythonApp.git)
cd PythonApp

# 2. Build the initial Docker image named ivolve1
docker build -t ivolve1 .

# 3. Run a container named container1, map port 5000, set environment variables (APP_NAME=iVolveApp1, APP_ENV=stg), and limit resources (1 CPU, 256MB memory)
docker run -d --name container1 -p 5000:5000 \
  -e APP_NAME=iVolveApp1 \
  -e APP_ENV=stg \
  --cpus="1.0" \
  --memory="256m" \
  ivolve1

# 4. Verify that the application is working by accessing http://localhost:5000
curl http://localhost:5000

# 5. Verify the environment variables configured inside container1
docker exec container1 env
--> nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab6/PythonApp$ curl http://localhost:5100
{"application":"iVolveApp1","environment":"stg","message":"Welcome to the Docker Env & Resource Management Lab!"}
nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab6/PythonApp$ docker exec container1 env
PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=29d0657a6ec9
APP_NAME=iVolveApp1
APP_ENV=stg
LANG=C.UTF-8
GPG_KEY=7169605F62C751356D054A26A821E680E5FA6305
PYTHON_VERSION=3.12.15
PYTHON_SHA256=c2c4321961fab0fb999d66e0cecf521c2ab3994c7992873ea99e306c1094fd5a
HOME=/root


# 6. Monitor container1's CPU and memory usage
docker stats container1 --no-stream
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab6/PythonApp$ CONTAINER ID   NAME         CPU %     MEM USAGE / LIMIT   MEM %     NET I/O         BLOCK I/O    PIDS
29d0657a6ec9   container1   0.06%     21.53MiB / 256MiB   8.41%     3.65kB / 829B   0B / 176kB   1

# 7. Stop and remove container1
docker stop container1
docker rm container1

# 8. Create an env.txt file with APP_NAME=iVolveApp2 and APP_ENV=prod
cat <<EOF> env.txt
APP_NAME=iVolveApp2
APP_ENV=prod
EOF

# 9. Run a container named container2, map port 5000, and load environment variables from env.txt
docker run -d --name container2 -p 5000:5000 --env-file env.txt ivolve1

# 10. Validate the environment variables loaded from env.txt inside container2
docker exec container2 env
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab6/PythonApp$ docker run -d --name container2 -p 5200:5000 --env-file env.txt ivolve1
e6fc1818da2436b4386d57652de65741f81e59286641e4079aeff97c79ba7c3f
nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab6/PythonApp$ docker exec container2 env
PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=e6fc1818da24
APP_NAME=iVolveApp2
APP_ENV=prod
LANG=C.UTF-8
GPG_KEY=7169605F62C751356D054A26A821E680E5FA6305
PYTHON_VERSION=3.12.15
PYTHON_SHA256=c2c4321961fab0fb999d66e0cecf521c2ab3994c7992873ea99e306c1094fd5a
HOME=/root


# 11. Stop and remove container2
docker stop container2
docker rm container2

# 12. Create/Update the Dockerfile to hardcode environment variables: APP_NAME=iVolveApp3 and APP_ENV=dev

# 13. Build a new Docker image with the hardcoded environment variables
docker build -t ivolve-hardcoded .

# 14. Run a container named container3, map port 5000, and validate the hardcoded environment variables
docker run -d --name container3 -p 5000:5000 ivolve-hardcoded
docker exec container3 env
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab6/PythonApp$ docker run -d --name container3 -p 5300:5000 ivolve-hardcoded
be831a9ac6a2962c4461f31358230d6b1205c4d1ef723237b7b4f199b6c31652
nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab6/PythonApp$ docker exec container3 env
PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=be831a9ac6a2
LANG=C.UTF-8
GPG_KEY=7169605F62C751356D054A26A821E680E5FA6305
PYTHON_VERSION=3.12.15
PYTHON_SHA256=c2c4321961fab0fb999d66e0cecf521c2ab3994c7992873ea99e306c1094fd5a
APP_NAME=iVolveApp3
APP_ENV=dev
HOME=/root


# 15. Clean up container3
docker stop container3
docker rm container3

