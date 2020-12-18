pipeline {  

  //Donde se va a ejecutar el Pipeline  
  agent {    
    label 'Slave_Induccion'  
  }  
  //Opciones específicas de Pipeline dentro del Pipeline  
  options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
    disableConcurrentBuilds()  
  }
  //Una sección que define las herramientas “preinstaladas” en Jenkins  
  tools {    
    jdk 'JDK8_Centos' 
    //Preinstalada en la Configuración del Master    
    gradle 'Gradle4.5_Centos' 
    //Preinstalada en la Configuración del Master  
  }

  //Aquí comienzan los “items” del Pipeline  
  stages{    

    stage('Checkout') {      
      steps{                
        echo "------------>Checkout<------------"      
        checkout scm
      }    
    }    

    stage('Compile & Unit Tests') {      
      steps{        
        echo "------------>Unit Tests<------------"      
      }    
    }    

    stage('Static Code Analysis') {      
      steps{        
        echo '------------>Análisis de código estático<------------'        
        withSonarQubeEnv('Sonar') {
          sh "${tool name: 'SonarScanner',type:'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner"        
        }      
      }    
    }    

    stage('Build') {      
      steps {        
        echo "------------>Build<------------"
        sh 'xcodebuild -scheme "Presentation" clean build CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED="NO"'

        //sh 'xcodeBuild -workspace ICTest/ICTest.xcodeproj/project.xcworkspace/ -scheme "ICTest" -destination \'platform=iOS Simulator,name=iPhone 11\''
      }    
    }  
  }

  post {    
    always {      
      echo 'This will always run'    
    }    
    success {      
      echo 'This will run only if successful'    
    }    
    failure {      
      echo 'This will run only if failed'    
    }    
    unstable {      
      echo 'This will run only if the run was marked as unstable'    
    }    
    changed {      
      echo 'This will run only if the state of the Pipeline has changed'      
      echo 'For example, if the Pipeline was previously failing but is now successful'    
    }  
  }
}
