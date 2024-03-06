pipeline{
  agent any
  tools{
    maven 'maven3'
    jdk   'jdk17'
  }            
  environment {
    SONARQUBE_HOME = tool 'SonarQube'
  }
  
  stages{
    steps('git checkout'){
      git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Su-love/Ekart'
    }
    steps('code compile'){
      sh 'mvn compile'
    }
    steps('unit test'){
      sh 'mvn test -DskipTests=true'
    }
    steps('Sonar analysis'){
      withSonarQubeEnv('sonar') {
        sh "${SONARQUBE_HOME}/bin/sonar-scanner"
      
    }
    }
  }
}
  
