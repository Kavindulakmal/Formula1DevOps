# Formula1 DevOps
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

You can confirm a successful Docker installation on Ubuntu by running the command
```
sudo docker run hello-world
```
Log in to Docker Hub from the Command Line
```
docker login

```
If you have this issue,
```
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/auth": dial unix /var/run/docker.sock: connect: permission denied
```
run these commands

```
sudo usermod -aG docker jenkins
sudo systemctl restart docker
sudo systemctl restart jenkins
groups jenkins

```
Adjust Permissions on Docker Socket (if necessary)
```
sudo chmod 666 /var/run/docker.sock
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
### --- Final Output ---
#### Pipeline stages

![stages](https://github.com/user-attachments/assets/f59d8053-1c23-46c8-8cfa-c1cc99be7af0)

#### Pipeline status

![pipeline status](https://github.com/user-attachments/assets/5905e7ba-042a-4872-9135-fcf1a76c2ea6)

#### Results

![result1](https://github.com/user-attachments/assets/1ebd5d58-4e04-45cb-85a0-e75f04b4b9de)

![Result2](https://github.com/user-attachments/assets/3754adae-de4b-4257-97c6-74215e0176a2)

# Contributing
Pull requests are welcomed. For major changes, please open an issue first to discuss what you would like to change. Thanks!

Happy Coding!!!

## Copyright
© KAVINDU™ | 2024
