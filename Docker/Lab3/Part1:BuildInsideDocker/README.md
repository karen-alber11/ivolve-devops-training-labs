# Lab 3: Creating a Docker Image for a Java Application
## Part 1: Build Inside Docker

### Clone App & Create Dockerfile
git clone https://github.com/lbrahim-Adel15/CalculatorMavenApp.git
cd CalculatorMavenApp

cat << 'EOF' > Dockerfile
FROM maven:3.8.5-openjdk-17
WORKDIR /app
RUN useradd -u 1000 appuser && chown -R appuser:appuser /app
USER appuser
COPY --chown=appuser:appuser . .
RUN mvn clean package
EXPOSE 8080
CMD ["java", "-jar", "target/calculator.jar"]
EOF

### Build Image & Check Size
docker build -t app1 .
docker images app1
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab3/Part1:BuildInsideDocker/CalculatorMavenApp$ docker images app1
                                                                                                                   i Info →   U  In Use
IMAGE         ID             DISK USAGE   CONTENT SIZE   EXTRA
app1:latest   1119c23fb4e6       1.31GB          452MB      

### Run Container & Verify
docker run -d --name container1 -p 8083:8080 app1
docker stop container1 && docker rm container1 && docker rmi app1

docker run --rm -it --name container1 app1
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab3/Part1:BuildInsideDocker/CalculatorMavenApp$ docker run --rm -it --name container1 app1
mkdir: cannot create directory ‘/root’: Permission denied
Can not write to /root/.m2/copy_reference_file.log. Wrong volume permissions? Carrying on ...
Enter first number: 20
Enter second number: 5
a = 20.0, b = 5.0
Sum: 25.0
Subtract: 15.0
Multiply: 100.0
Divide: 4.0

### Cleanup
docker stop container1 && docker rm container1 && docker rmi app1 

###Tree
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab3/Part1:BuildInsideDocker$ tree
.
├── CalculatorMavenApp
│   ├── Dockerfile
│   ├── pom.xml
│   └── src
│       ├── main
│       │   └── java
│       │       └── com
│       │           └── example
│       │               └── calculator
│       │                   ├── Calculator.java
│       │                   └── Main.java
│       └── test
│           └── java
│               └── com
│                   └── example
│                       └── calculator
│                           └── CalculatorTest.java
└── README.md

12 directories, 6 files
