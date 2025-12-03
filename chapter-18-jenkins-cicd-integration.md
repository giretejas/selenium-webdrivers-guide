# Chapter 18: Jenkins/CI-CD Integration

### 18.1 Jenkins Job Setup

**Jenkinsfile (Declarative Pipeline)**:

```groovy
pipeline {
    agent any
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '30'))
        timeout(time: 2, unit: 'HOURS')
        timestamps()
    }
    
    parameters {
        choice(name: 'BROWSER', choices: ['Chrome', 'Firefox'], description: 'Browser to test')
        choice(name: 'ENVIRONMENT', choices: ['DEV', 'QA', 'STAGING'], description: 'Test environment')
        booleanParam(name: 'PARALLEL_EXECUTION', defaultValue: true, description: 'Run tests in parallel')
    }
    
    stages {
        stage('Setup') {
            steps {
                echo 'Setting up test environment...'
                checkout scm
                sh 'mvn clean'
            }
        }
        
        stage('Run Tests') {
            steps {
                echo "Running tests on ${params.BROWSER} in ${params.ENVIRONMENT}"
                sh '''
                    mvn test \
                        -Dbrowser=${BROWSER} \
                        -Denvironment=${ENVIRONMENT} \
                        -Dparallel=${PARALLEL_EXECUTION}
                '''
            }
        }
        
        stage('Generate Report') {
            steps {
                echo 'Generating test reports...'
                sh 'mvn allure:report'
            }
        }
    }
    
    post {
        always {
            // Archive test results
            junit 'target/surefire-reports/*.xml'
            
            // Publish Allure report
            publishHTML([
                reportDir: 'target/site/allure-maven-plugin',
                reportFiles: 'index.html',
                reportName: 'Allure Report'
            ])
            
            // Archive screenshots
            archiveArtifacts artifacts: 'screenshots/**/*.png', allowEmptyArchive: true
        }
        
        failure {
            echo 'Tests failed!'
            emailext(
                subject: 'Test Execution Failed',
                body: 'Build ${BUILD_NUMBER} failed. Check console output at ${BUILD_URL}',
                to: '${DEFAULT_RECIPIENTS}'
            )
        }
        
        success {
            echo 'Tests passed!'
        }
    }
}
```

### 18.2 Maven Configuration for CI/CD

**pom.xml with CI/CD Profiles**:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.automation</groupId>
    <artifactId>selenium-tests</artifactId>
    <version>1.0.0</version>
    
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <selenium.version>4.15.0</selenium.version>
        <testng.version>7.8.1</testng.version>
    </properties>
    
    <dependencies>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>${selenium.version}</version>
        </dependency>
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>${testng.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>io.github.bonigarcia</groupId>
            <artifactId>webdrivermanager</artifactId>
            <version>5.6.3</version>
        </dependency>
        <dependency>
            <groupId>io.qameta.allure</groupId>
            <artifactId>allure-testng</artifactId>
            <version>2.20.1</version>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0-M9</version>
                <configuration>
                    <suiteXmlFiles>
                        <suiteXmlFile>testng.xml</suiteXmlFile>
                    </suiteXmlFiles>
                    <argLine>-Dbrowser=${browser} -Denvironment=${environment}</argLine>
                </configuration>
            </plugin>
            
            <plugin>
                <groupId>io.qameta.allure</groupId>
                <artifactId>allure-maven</artifactId>
                <version>2.13.0</version>
            </plugin>
        </plugins>
    </build>
    
    <profiles>
        <profile>
            <id>ci-chrome</id>
            <properties>
                <browser>chrome</browser>
                <headless>true</headless>
            </properties>
        </profile>
        
        <profile>
            <id>ci-firefox</id>
            <properties>
                <browser>firefox</browser>
                <headless>true</headless>
            </properties>
        </profile>
        
        <profile>
            <id>dev</id>
            <properties>
                <environment>dev</environment>
                <base.url>http://localhost:8080</base.url>
            </properties>
        </profile>
        
        <profile>
            <id>qa</id>
            <properties>
                <environment>qa</environment>
                <base.url>https://qa.example.com</base.url>
            </properties>
        </profile>
    </profiles>
</project>
```

### 18.3 GitHub Actions Integration

```yaml
name: Selenium Automation Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        browser: [chrome, firefox]
        environment: [qa, staging]
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'
    
    - name: Cache Maven packages
      uses: actions/cache@v2
      with:
        path: ~/.m2
        key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
    
    - name: Run Tests
      run: |
        mvn test \
          -Dbrowser=${{ matrix.browser }} \
          -Denvironment=${{ matrix.environment }} \
          -Dheadless=true
    
    - name: Upload Test Results
      if: always()
      uses: actions/upload-artifact@v2
      with:
        name: test-results-${{ matrix.browser }}-${{ matrix.environment }}
        path: target/surefire-reports/
    
    - name: Publish Test Report
      if: always()
      uses: dorny/test-reporter@v1
      with:
        name: Test Results
        path: 'target/surefire-reports/*.xml'
        reporter: 'java-testng'
```

### 18.4 Best Practices for CI/CD
- Use environment variables for sensitive data
- Implement proper logging and reporting
- Use version control for all test code
- Schedule regular test runs
- Monitor test execution metrics
- Set up notifications for failures
- Maintain clean CI/CD pipeline
- Use parameterized jobs for flexibility
