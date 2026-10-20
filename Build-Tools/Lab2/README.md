# Lab 2: Building and Packaging Go Application with Gradle

### Install Gradle & Check Version
sudo apt install -y gradle
gradle -v

### Clone Source Code
git clone https://github.com/Ibrahim-Adel15/CalculatorGradleApp.git
cd CalculatorGradleApp

### Update build.gradle to use Java 21 instead of 17
sed -i 's/languageVersion = JavaLanguageVersion.of(17)/languageVersion = JavaLanguageVersion.of(21)/g' build.gradle

### Run Unit Tests
gradle test
-->nti@nti-virtual-machine:~/DevOps-Labs/Build-Tools/Lab2/CalculatorGradleApp$ /opt/gradle/gradle-8.5/bin/gradle test

BUILD SUCCESSFUL in 2m 1s
3 actionable tasks: 3 executed

### Build App (Generates build/libs/calculator.jar)
gradle build
-->nti@nti-virtual-machine:~/DevOps-Labs/Build-Tools/Lab2/CalculatorGradleApp$ /opt/gradle/gradle-8.5/bin/gradle build

BUILD SUCCESSFUL in 29s
7 actionable tasks: 4 executed, 3 up-to-date

### Run Application
java -jar build/libs/calculator.jar --server.port=8082
-->nti@nti-virtual-machine:~/DevOps-Labs/Build-Tools/Lab2/CalculatorGradleApp$ java -jar build/libs/calculator.jar --server.port=8082
Enter first number: 20
Enter second number: 5
a = 20.0, b = 5.0
Sum: 25.0
Subtract: 15.0
Multiply: 100.0
Divide: 4.0

### Verify Application
curl http://localhost:8082

###Tree
-->nti@nti-virtual-machine:~/DevOps-Labs/Build-Tools/Lab2$ tree
.
└── CalculatorGradleApp
    ├── build
    │   ├── classes
    │   │   └── java
    │   │       ├── main
    │   │       │   └── com
    │   │       │       └── example
    │   │       │           └── calculator
    │   │       │               ├── Calculator.class
    │   │       │               └── Main.class
    │   │       └── test
    │   │           └── com
    │   │               └── example
    │   │                   └── calculator
    │   │                       └── CalculatorTest.class
    │   ├── distributions
    │   │   ├── calculator-1.0.0.tar
    │   │   └── calculator-1.0.0.zip
    │   ├── generated
    │   │   └── sources
    │   │       ├── annotationProcessor
    │   │       │   └── java
    │   │       │       ├── main
    │   │       │       └── test
    │   │       └── headers
    │   │           └── java
    │   │               ├── main
    │   │               └── test
    │   ├── libs
    │   │   └── calculator.jar
    │   ├── reports
    │   │   └── tests
    │   │       └── test
    │   │           ├── classes
    │   │           │   └── com.example.calculator.CalculatorTest.html
    │   │           ├── css
    │   │           │   ├── base-style.css
    │   │           │   └── style.css
    │   │           ├── index.html
    │   │           ├── js
    │   │           │   └── report.js
    │   │           └── packages
    │   │               └── com.example.calculator.html
    │   ├── scripts
    │   │   ├── calculator
    │   │   └── calculator.bat
    │   ├── test-results
    │   │   └── test
    │   │       ├── binary
    │   │       │   ├── output.bin
    │   │       │   ├── output.bin.idx
    │   │       │   └── results.bin
    │   │       └── TEST-com.example.calculator.CalculatorTest.xml
    │   └── tmp
    │       ├── compileJava
    │       │   └── previous-compilation-data.bin
    │       ├── compileTestJava
    │       │   └── previous-compilation-data.bin
    │       ├── jar
    │       │   └── MANIFEST.MF
    │       └── test
    ├── build.gradle
    ├── gradle-8.5-bin.zip
    ├── settings.gradle
    └── src
        ├── main
        │   └── java
        │       └── com
        │           └── example
        │               └── calculator
        │                   ├── Calculator.java
        │                   └── Main.java
        └── test
            └── java
                └── com
                    └── example
                        └── calculator
                            └── CalculatorTest.java

51 directories, 27 files
