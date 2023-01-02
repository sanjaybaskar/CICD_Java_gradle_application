pipeline {
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages {
        stage("Sonar code quality check"){
            steps{
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }
		    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }
	    }
        stage("Docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                        sh '''
                         docker built -t 65.2.182.139:8083/springapp:${VERSION} .    
                         docker login -u -p $docker_password 65.2.182.139:8083
                         docker push 65.2.182.139:8083/springapp${VERSION}
                         docker rmi 65.2.182.139:8083/springapp${VERSION}
                        '''
                        }
                }
            }
        }
       
    }

}

