pipeline {
    environment { 
        registry = "imenelhakim/timesheetimen" 
        registryCredential = 'dockerHub' 
        dockerImage = '' 
    }
    agent any
    stages{
        stage('clone and clean repo'){
            steps {
                
      
                bat "git clone https://github.com/imenelhakim/timesheetimen"
                bat "mvn clean -f timesheetimen "
            }
        }
        stage('Test'){
            steps{ bat "mvn test -f timesheetimen"}
        }
        stage('Deploy'){
            steps {
                bat "mvn package -f timesheetimen"
                bat "mvn deploy -f timesheetimen"
                bat "mvn sonar:sonar -f timesheetimen -Dsonar.host.url=http://localhost:9000 -Dsonar.login=dfef96fa643c0fa5788b20500106a5cb5fc60516"
            }
        }
        stage('Email'){
            steps{
                //extended email
                //emailext body: 'You just launched a job !', subject: 'Hey Imen !', to: 'imen.devops@gmail.com'
                mail bcc: '', body: 'Hey From Jenkins !', cc: '', from: 'imen.elhakim@esprit.tn', replyTo: '', subject: 'Hey From Jenkins !', to: 'imen.devops@gmail.com'
            }
        }
        stage('Building our image') {
            steps{
                script {
                    dir("timesheetimen"){
                        dockerImage = docker.build registry + ":$BUILD_NUMBER"
                    }
                }
            }
        }
        stage('Deploy our image') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Cleaning up') {
            steps {
                bat "docker rmi $registry:$BUILD_NUMBER"
            }
        }
    }
    post{
            always{
            cleanWs()
        }
    }
}
