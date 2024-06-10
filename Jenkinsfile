pipeline{
    agent any
    
    tools{
        jdk "jdk17"
        nodejs "node16"    
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = 'reddit-clone-app'
        RELEASE = '1.0.0'
        DOCKER_USER = 'biswajitt'
        DOCKER_PASS = 'docker'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}" + "-" + "${BUILD_NUMBER}"
   }
    stages{
        stage("CleanWorkspace"){
            steps{
                cleanWs()
            }
        }

        stage("GITCheckout"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/KRYPTONITE-DEVOPS-CICD/cicd-proj-reddit-clone.git'
            }
        }

        stage("sonar-server"){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=reddit-clone-cicd \
                    -Dsonar.projectKey=reddit-clone-cicd'''
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-server'
                }
            }
        }
        stage("Install Dependencies"){
            steps{
                sh "npm install"
            }
        }

        stage("TRIVY SCAN FILESYSTEM"){
            steps{
                sh "trivy fs . > trivyFS.txt"
            }
        }

        stage("BUILD and PUSH DOCKER"){
            steps{
                script{
                    docker.withRegistry('',DOCKER_PASS){
                        docker_image = docker.build("${IMAGE_NAME}")
                    }
                        
                    docker.withRegistry('',DOCKER_PASS){
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                    
                }
            }
        }
    }
}
