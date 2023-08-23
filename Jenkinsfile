pipeline {
	agent any
	stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'  //test
            }
        }   
		
//-------------------------------------------
	stage('UNIT test & jacoco ') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }

    }		
//----------------------------------------
  stage('Mutation Tests - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
        post { 
         always { 
           pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
         }
       }
    }
//-------------------------------------------
	stage('Docker Build and Push') {
      steps {
        withCredentials([string(credentialsId: 'docker_hub_password', variable: 'DOCKER_HUB_PASSWORD')]) {
          sh 'sudo docker login -u yanishub1 -p $DOCKER_HUB_PASSWORD'
          sh 'printenv'
          sh 'sudo docker build -t yanishub1/devops-app:""$GIT_COMMIT"" .'
          sh 'sudo docker push yanishub1/devops-app:""$GIT_COMMIT""'
        }

 

      }
    }	
		
	}
}
