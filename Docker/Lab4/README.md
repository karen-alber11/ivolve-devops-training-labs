# Lab 4: Docker Image Optimization and Export/Load

## Sequential Commands & Workflow

```bash
# 1. Clone the application repository and navigate into the project directory
git clone [https://github.com/Ibrahim-Adel15/ImageOptimization-.git](https://github.com/Ibrahim-Adel15/ImageOptimization-.git)
cd ImageOptimization-

# 2. Build the initial unoptimized image named image1
docker build -t image1 .

# 3. Check the size of image1
docker images image1

# 4. Run a container named container1 from image1, mapping host port 8070 to container port 8080
docker run -d --name container1 -p 8070:8080 image1
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab4/ImageOptimization-$ docker run -d --name container1 -p 8070:80 image1
eeaf568d0b3665448e0eef52824ac9e9fd6d54abc5a2a85396acec498f00ddb2


# 5. Verify that the application is working properly
curl http://localhost:8070

# 6. Stop and remove container1
docker stop container1
docker rm container1
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab4/ImageOptimization-$ docker stop container1
docker rm container1
container1
container1

# 7. Create/Update the Dockerfile to use a multi-stage build with a non-root security user (see Dockerfile)

# 8. Build the optimized image named image2
docker build -t image2 .

# 9. Compare the image sizes of image1 and image2
docker images | grep image
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab4/ImageOptimization-$ docker images | grep image
image1:latest                                                                          541169d884cb        671MB          239MB        
image2:latest                                                                          08d0aa27a5f0        294MB         85.7MB        
nti@nti-virtual-machi

# 10. Run a container named container2 from the optimized image2
docker run -d --name container2 -p 8070:8080 image2

# 11. Verify that the application works and runs as a non-root user
docker exec container2 whoami
curl http://localhost:8070

# 12. Stop and remove container2
docker stop container2
docker rm container2

# 13. Export/Save image2 as a TAR archive named lab-image.tar
docker save -o lab-image.tar image2

# 14. Delete image2 from the local Docker host
docker rmi image2

# 15. Load the image back into Docker from the TAR archive
docker load -i lab-image.tar

# 16. Verify that the loaded image has been successfully restored on the local host
docker images | grep image2
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab4/ImageOptimization-$ docker save -o lab-image.tar image2
nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab4/ImageOptimization-$ docker rmi image2
docker load -i lab-image.tar
docker images | grep image2
Untagged: image2:latest
Deleted: sha256:08d0aa27a5f04757818dd7b2fbbdb27103e44d6c6a7ffb3b50a4cebcf405a24b
Loaded image: image2:latest
image2:latest                                                                          08d0aa27a5f0        294MB         85.7MB        
nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab4/ImageOptimization-$ docker images | grep image2
image2:latest                                                                          08d0aa27a5f0        294MB         85.7MB  
