# Lab 8: Docker Compose Multi-Container Stack & Deployment

## Sequential Commands & Workflow

```bash
# 1. Navigate to the Lab 8 directory containing your application code
cd ~/DevOps-Labs/Docker/Lab8/iVolveApp

# 2. Create and configure the docker-compose.yml file defining the Node.js application and MySQL services (see compose configuration)

# 3. Build and start the multi-container stack in detached mode
docker compose up -d --build
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab8/iVolveApp$ docker compose up -d --build
 
[+] Building 44.7s (12/12) FINISHED                                                                                                    
 => [internal] load local bake definitions                                                                                        0.0s
 => => reading from stdin 530B                                                                                                    0.0s
 => [internal] load build definition from Dockerfile                                                                              0.1s
 => => transferring dockerfile: 162B                                                                                              0.0s
 => [internal] load metadata for docker.io/library/node:18-alpine                                                                 2.3s
 => [internal] load .dockerignore                                                                                                 0.1s
 => => transferring context: 2B                                                                                                   0.0s
 => [1/5] FROM docker.io/library/node:18-alpine@sha256:8d6421d663b4c28fd3ebc498332f249011d118945588d0a35cb9bc4b8ca09d9e           0.4s
 => => resolve docker.io/library/node:18-alpine@sha256:8d6421d663b4c28fd3ebc498332f249011d118945588d0a35cb9bc4b8ca09d9e           0.4s
 => [internal] load build context                                                                                                 0.4s
 => => transferring context: 223.03kB                                                                                             0.2s
 => CACHED [2/5] WORKDIR /app                                                                                                     0.0s
 => [3/5] COPY package.json ./                                                                                                    0.2s
 => [4/5] RUN npm install                                                                                                        33.1s
 => [5/5] COPY . .                                                                                                                0.7s 
 => exporting to image                                                                                                            6.7s 
 => => exporting layers                                                                                                           2.7s 
 => => exporting manifest sha256:78bc490d3ac76225adf6e1ab193d63fd37c25cc46c051603ba54e81699565d85                                 0.0s 
 => => exporting config sha256:ce72a4968fd858b3f96206c0f5313d6d55aef43b2db8e6f6c3c8589ddc30195b                                   0.1s 
 => => exporting attestation manifest sha256:812a85909aea82e98418d8e7ea5ddae8310fdbc1932daf771385562085f12be2                     0.1s 
 => => exporting manifest list sha256:343eadb409e6f10b6bce9c6b046147641a93a1d1838ddcdd1b233ffe99784400                            0.1s
 => => naming to docker.io/library/ivolveapp-app:latest                                                                           0.0s
 => => unpacking to docker.io/library/ivolveapp-app:latest                                                                        3.5s
 => resolving provenance for metadata file                                                                                        0.1s
[+] up 6/6
 ✔ Image ivolveapp-app          Built                                                                                             45.3s
 ✔ Network ivolveapp_ivolve-net Created                                                                                            0.3s
 ✔ Volume ivolveapp_db_data     Created                                                                                            0.0s
 ✔ Volume ivolveapp_app_logs    Created                                                                                            0.0s
 ✔ Container mysql_db           Started                                                                                            2.6s
 ✔ Container node_app           Started  

# 4. Check the status of the running containers in the stack
docker compose ps
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab8/iVolveApp$ docker compose ps 
NAME       IMAGE           COMMAND                  SERVICE    CREATED          STATUS          PORTS
mysql_db   mysql:8.0       "docker-entrypoint.s…"   mysql_db   29 seconds ago   Up 27 seconds   3306/tcp, 33060/tcp
node_app   ivolveapp-app   "docker-entrypoint.s…"   app        28 seconds ago   Up 25 seconds   0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp

# 5. View logs of the Node.js app to verify database connection success
docker compose logs -f app
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab8/iVolveApp$ docker compose logs -f app
WARN[0000] /home/nti/DevOps-Labs/Docker/Lab8/iVolveApp/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | 🔁 Retrying in 5 seconds...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | 🔁 Retrying in 5 seconds...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | 🔁 Retrying in 5 seconds...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | 🔁 Retrying in 5 seconds...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ❌ Connection error: connect ECONNREFUSED 172.18.0.2:3306
node_app  | 🔁 Retrying in 5 seconds...
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ✅ Connected to MySQL and 'ivolve' DB found.
node_app  | 🚀 Server started on http://0.0.0.0:3000
node_app  | 🔄 Attempting to connect to MySQL...
node_app  | ✅ Connected to MySQL and 'ivolve' DB found.
node_app  | 🚀 Server started on http://0.0.0.0:3000


# 6. (Optional) If an initial database connection timeout occurs, restart the app service
docker compose restart app

# 7. List local images to verify the generated build image name (e.g., ivolveapp-app)
docker images
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab8/iVolveApp$ docker images
                                                                                                                   i Info →   U  In Use
IMAGE                                                                                  ID             DISK USAGE   CONTENT SIZE   EXTRA
app1:latest                                                                            1119c23fb4e6       1.31GB          452MB        
backend-image:latest                                                                   eb0fd19aaa49        202MB         49.3MB    U   
frontend-image:latest                                                                  479be75e1c71        202MB         49.3MB    U   
image1:latest                                                                          541169d884cb        671MB          239MB        
image2:latest                                                                          08d0aa27a5f0        294MB         85.7MB        
ivolve-app:v2                                                                          06813dfe6baa       1.46GB          371MB        
ivolve-hardcoded:latest                                                                fb7c1b2ce2d7        198MB         48.3MB    U   
ivolve1:latest                                                                         c230e3e0742e        198MB         48.3MB    U   
ivolveapp-app:latest                                                                   343eadb409e6        221MB         51.4MB    U   
kindest/node@sha256:047357ac0cfea04663786a612ba1eaba9702bef25227a794b52890dd8bcd692e   047357ac0cfe       1.41GB          416MB    U   
maven:3.8.5-openjdk-17                                                                 3a9c30b3af62       1.26GB          432MB        
mysql:8.0                                                                              7dcddc01f13b        1.1GB          249MB    U   
notification-image:latest                                                              96a9c5190ed6        198MB         48.3MB    U   
trivy-lab:v1                                                                           46a5201fd445       1.46GB          371MB    


# 8. Log in to your Docker Hub account
docker login

# 9. Tag your local application image with your Docker Hub username and repository name
docker tag ivolveapp-app:latest karenalber/ivolve-node-app:latest

# 10. Push the tagged image to Docker Hub
docker push karenalber/ivolve-node-app:latest
--> nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab8/iVolveApp$ docker tag ivolveapp-app:latest karenalber/ivolve-node-app:latest        
nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab8/iVolveApp$ docker push karenalber/ivolve-node-app:latest
The push refers to repository [docker.io/karenalber/ivolve-node-app]
44136fa355b3: Pushed 
0f0272d2b1a8: Pushed 
dd71dde834b5: Pushed 
f18232174bc9: Pushed 
1e5a4c89cee5: Pushed 
25ff2da83641: Pushed 
1cfbab88dca8: Pushed 
d396e03badbe: Pushed 
94212d9c909a: Pushed 
79d072b7a3f7: Pushed 
latest: digest: sha256:343eadb409e6f10b6bce9c6b046147641a93a1d1838ddcdd1b233ffe99784400 size: 856

# 11. Stop and tear down the multi-container stack when finished
docker compose down
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab8/iVolveApp$ docker compose down
WARN[0000] /home/nti/DevOps-Labs/Docker/Lab8/iVolveApp/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] down 3/3
 ✔ Container node_app           Removed                                                                                           11.1s
 ✔ Container mysql_db           Removed                                                                                            2.2s
 ✔ Network ivolveapp_ivolve-net Removed

<img width="2846" height="1456" alt="image" src="https://github.com/user-attachments/assets/6d1f689d-ff32-4870-9fb9-c3e4a9cbbaca" />

