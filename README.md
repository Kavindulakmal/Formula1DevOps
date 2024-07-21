# Formula1DevOps
## Jenkins Practice Project with Docker and Microsoft Azure

### --- INSTALL JENKINS ---
```
sudo apt install openjdk-11-jre
```
```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```
```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
```
sudo apt-get update -y
```
```
sudo apt-get install jenkins -y
```
```
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```
### --- INSTALL DOCKER ---

```
sudo apt install docker.io
```
```
sudo systemctl enable docker
```
```
sudo systemctl status docker
```
```
sudo systemctl start docker
```

### Pipelline Script

```
pipeline {
    agent any
    
    tools{
        nodejs 'nodejs-22'
        
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Kavindulakmal/Formula1DevOps.git'
            }
        }
        
        stage('install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('OWASP Dependency check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry([credentialsId: '<ourid>']) {
                        sh 'docker build -t demonodejs .'
                        sh 'docker tag demonodejs wicklak/nodejs:latest'
                        sh 'docker push wicklak/nodejs:latest'
                     }
                        
                        

                    
                }
            }
        }
        
        stage('Docker Deploy') {
            steps {
                script {
                    withDockerRegistry([credentialsId: '<yourid>']) {
                        sh 'docker run -d --name demof1 -p 8081:8081 wicklak/nodejs:latest'
                        
                     }
                        
                        

                    
                }
            }
        }
    }
}


```
