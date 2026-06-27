# jenkins-Docker-project
```
java-maven-demo/
├── pom.xml
├── Jenkinsfile
├── Dockerfile
└── src
    ├── main
    │   └── java
    │       └── com
    │           └── prem
    │               └── App.java
    └── test
        └── java
            └── com
                └── prem
                    └── AppTest.java
```

### pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.prem</groupId>
    <artifactId>java-maven-demo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>

</project>
```

### App.java

```java
package com.prem;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello from Maven!");
    }
}
```

### AppTest.java

```java
package com.prem;

public class AppTest {

    public static void main(String[] args) {
        System.out.println("Test Executed Successfully");
    }
}
```

### Jenkinsfile

```groovy
pipeline {
agent any

stages {

    stage('Git Checkout') {
        steps {
            git branch: 'main',
                url: 'https://github.com/Premkumar734/jenkins-Docker-project.git'
        }
    }

    stage('Build') {
        steps {
            echo 'Building the project...'
            sh 'mvn clean package -DskipTests'
        }
    }

    stage('Test') {
        steps {
            echo 'Running tests...'
            sh 'mvn test'
        }
    }

    stage('Deploy') {
        steps {
            echo 'Deploying application...'
            
            // Example deployment (local container/server)
            sh '''
                echo "Stopping old container (if exists)"
                docker stop myapp || true
                docker rm myapp || true

                echo "Building Docker image"
                docker build -t myapp .

                echo "Running new container"
                docker run -d --name myapp -p 8080:8080 myapp
            '''
        }
    }
}

post {
    success {
        echo 'Pipeline completed successfully!'
    }
    failure {
        echo 'Pipeline failed. Check logs.'
    }
}
}
```

### Dockerfile

```dockerfile
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/java-maven-demo-1.0-SNAPSHOT.jar app.jar

CMD ["java","-jar","app.jar"]
```
