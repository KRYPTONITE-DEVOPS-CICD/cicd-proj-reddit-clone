pipeline{
    agent any
    
    tools{
        jdk "jdk17"
        nodejs "node16"    
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages{
        stage("CleanWorkspace")
        {
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
                withSonarQubeEnv('SonarQube-Server') {
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

}
