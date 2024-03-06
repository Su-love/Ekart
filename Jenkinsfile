pipeline{
  agent any
  tools{
    maven 'maven3'
    jdk   'java17'
  }            
  environment {
    SONARQUBE_HOME = tool 'sonar-scanner'
  }
  
  stages{
    stage('git checkout'){
      steps{
        git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Su-love/Ekart'
      }
    }
    stage('code compile'){
      steps{
        sh 'mvn compile'
      }
    }
    stage('unit test'){
      steps{
        sh 'mvn test -DskipTests=true'
      }
    }
        stage("SonarQube Analysis"){
           steps {
	           script {
		        withSonarQubeEnv(credentialsId: 'sonar-jenkins') { 
                        sh "mvn sonar:sonar"
		        }
	           }	
           }
       }
       stage("Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-jenkins'
                }	
            }

        }
    }
}
  
