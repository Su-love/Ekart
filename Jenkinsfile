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
    DOCKER_USER = "sulove"
    DOCKER_PASS = 'docker-login'
    IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
    IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    NEXUS_URL = "http://192.168.95.5:8081/"
    REPOSITORY_ID = "maven-snapshots"
  }
  
  stages{
   stage("Cleanup Workspace"){
        steps {
        cleanWs()
         }
    }
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
                    sh 'mvn  package -DskipTests=true'
            }
       }
       
       stage("deploy to nexus"){
           steps {
                    withMaven(globalMavenSettingsConfig: 'nexus-repo', jdk: 'java17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                   sh 'mvn deploy -DskipTests=true'
		}
              }	
         }
        stage("Build Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build("${IMAGE_NAME}", "-f docker/Dockerfile .")
                    }  
		}
	    }
	}
    }
}
  
