# Lab 3: Creating a Docker Image for a Java Application
## Part 2: Build Locally

### Copy Built Maven Locally from Lab1
cp -r ~/DevOps-Labs/Build-Tools/Lab1/CalculatorMavenApp/target ~/DevOps-Labs/Docker/Lab3/Part2:BuildLocally/CalculatorMavenApp/

### Create Dockerfile
cat << 'EOF' > Dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
RUN useradd -u 1000 appuser && chown -R appuser:appuser /app
USER appuser
COPY --chown=appuser:appuser target/calculator.jar /app/calculator.jar
EXPOSE 8080
CMD ["java", "-jar", "calculator.jar"]
EOF

### Build Image & Check Size
docker build -t app2 .
docker images app2

### Run Container & Verify
docker run --rm -it --name container2 app2

### Cleanup
docker stop container2 && docker rm container2 && docker rmi app2

###Note
Here is the structure but I faced so many errors while building the mvn package because of mismatched plugins versions. Task was not successful.
