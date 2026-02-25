pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'master', url: 'https://github.com/devops-cloud-linux-spec/Amazon-Front-Page.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Amazon \
                    -Dsonar.projectKey=Amazon '''
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
	stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry([credentialsId: 'redhatgrras']){
                       sh "docker build -t amazon ."
                       sh "docker tag amazon redhatgrras/amazon:latest "
                       sh "docker push redhatgrras/amazon:latest "
                    }
                }
            }
        }
	stage('Deploy to container'){
            steps{
                sh 'docker run -d --name amazon -p 3000:3000 redhatgrras/amazon:latest'
            }
        }
    }
}
