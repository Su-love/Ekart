pipeline{
  agent any
  tools{
    maven 'maven3'
    jdk   'java17'
  }            
  environment {
    SONARQUBE_HOME = tool 'sonar-scanner'
    App_name = "ecart"
    Release = "1.0.0"
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
       stage('owsap dependency check'){
          steps{
            script {
		    dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
		    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
	   	 }
		    
      	   }
	}
       stage("Build-artificat"){
           steps {
                    sh 'mvn clean -DskipTests=true'
            }
       }
       stage("deploy to nexus"){
           steps {
                    withMaven(globalMavenSettingsConfig: 'nexus-repo', jdk: 'java17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                   sh 'mvn deploy -DskipTests=true'
		}
              }	
         }
       stage("docker build"){
           steps {
		   scripts{
                    	withDockerRegistry(credentialsId: 'docker-login', url: 'https://hub.docker.com/repositories/sulove') {
    			sh "docker build -t sulove/"${App_name}":"${Release}-${BUILD_NUMBER}" -f docker/Dockerfile ."
		}
              }
	   }
       }  
    }
}
  
