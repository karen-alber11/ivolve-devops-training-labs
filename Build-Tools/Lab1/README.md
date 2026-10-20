# Lab 1: Building and Packaging Java Application with Maven

### Prerequisites & Version Check
sudo apt update && sudo apt install -y openjdk-17-jdk maven
java -version
mvn -version

### Clone Source Code
git clone https://github.com/lbrahim-Adel15/CalculatorMavenApp.git
cd CalculatorMavenApp

### Run Unit Tests
mvn test

### Build App (Generates target/calculator.jar)
mvn package

### Run Application
java -jar target/calculator.jar --server.port=8081

-->nti@nti-virtual-machine:~/DevOps-Labs/Build-Tools/Lab1/CalculatorMavenApp$ java -jar target/calculator.jar --server.port=8081
Enter first number: 20
Enter second number: 5
a = 20.0, b = 5.0
Sum: 25.0
Subtract: 15.0
Multiply: 100.0
Divide: 4.0


### Verify Application
curl http://localhost:8081

### Tree
-->nti@nti-virtual-machine:~/DevOps-Labs/Build-Tools/Lab1$ tree
.
├── CalculatorMavenApp
│   ├── pom.xml
│   ├── src
│   │   ├── main
│   │   │   └── java
│   │   │       └── com
│   │   │           └── example
│   │   │               └── calculator
│   │   │                   ├── Calculator.java
│   │   │                   └── Main.java
│   │   └── test
│   │       └── java
│   │           └── com
│   │               └── example
│   │                   └── calculator
│   │                       └── CalculatorTest.java
│   └── target
│       ├── calculator.jar
│       ├── classes
│       │   └── com
│       │       └── example
│       │           └── calculator
│       │               ├── Calculator.class
│       │               └── Main.class
│       ├── generated-sources
│       │   └── annotations
│       ├── generated-test-sources
│       │   └── test-annotations
│       ├── maven-archiver
│       │   └── pom.properties
│       ├── maven-status
│       │   └── maven-compiler-plugin
│       │       ├── compile
│       │       │   └── default-compile
│       │       │       ├── createdFiles.lst
│       │       │       └── inputFiles.lst
│       │       └── testCompile
│       │           └── default-testCompile
│       │               ├── createdFiles.lst
│       │               └── inputFiles.lst
│       ├── surefire-reports
│       │   ├── com.example.calculator.CalculatorTest.txt
│       │   └── TEST-com.example.calculator.CalculatorTest.xml
│       └── test-classes
│           └── com
│               └── example
│                   └── calculator
│                       └── CalculatorTest.class
└── README.md

33 directories, 16 files
