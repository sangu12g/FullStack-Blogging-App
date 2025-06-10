pipeline {
    agent any
    
    tools { 
        jdk 'jdk17'
        maven 'maven3'
        
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('git checkout') {
            steps {
              git branch: 'main', url: 'https://github.com/sangu12g/FullStack-Blogging-App.git'
            }
        }
         stage('Compile') {
            steps {
               sh "mvn compile"
            }
        }
         stage('test') {
            steps {
                sh "mvn test"
            }
        }
         stage('Trivy Fs Scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
            }
        }
         stage('Sonarqube scan') {
            steps {
                withSonarQubeEnv('sonar-server') {
                 sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-App -Dsonar.projectKey=Blogging-App \
                        -Dsonar.java.binaries=target '''
}
            }
        }
         stage('Build') {
            steps {
                sh "mvn package"
            }
        }
         stage('Publish artifacts') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                 sh "mvn deploy"
                }
               
            }
        }
         stage('Docker Image build & tag') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                sh " docker build -t sangu12g/bloggingapp:latest ."
           }
                }
                }
        }
        
                 stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format table -o image.html sangu12g/bloggingapp:latest"
            }
        }
                 stage('Docker Image push') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                sh " docker push sangu12g/bloggingapp:latest "
           }
                }
            }
        }
        
        
    }
}
