pipeline {  

  //Donde se va a ejecutar el Pipeline  
  agent {    
    label 'Slave_Mac'  
  }  
  //Opciones específicas de Pipeline dentro del Pipeline  
  options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
    disableConcurrentBuilds()  
  }

  //Aquí comienzan los “items” del Pipeline  
  stages{    

    /*stage('Checkout') {      
      steps{                
        echo "------------>Checkout<------------"      
        checkout scm
      }    
    }*/    
	
	stage('Build') {      
      steps {        
        echo "------------>Build<------------"
        // sh 'xcodebuild -scheme "ICTest" clean build -destination \'platform=iOS Simulator,name=iPhone 11\'' 
		sh 'xcodebuild -scheme "ICTest" clean build CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED="NO"'		
      }    
    }  

    stage('Unit Tests') {      
      steps{        
        echo "------------>Unit Tests<------------"    
		sh 'xcodebuild -scheme "ICTest" -enableCodeCoverage YES -configuration Debug CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED="NO" test | tee build/xcodebuild-test.log | xcpretty -r junit --output build/reports/junit.xml'
        sh 'slather coverage --scheme "ICTest" --cobertura-xml --output-directory build/coverage "ICTest.xcodeproj"'		
      }    
    }    

    /*stage('Static Code Analysis') {      
      steps{        
        echo '------------>Análisis de código estático<------------'        
        withSonarQubeEnv('Sonar') {
          sh "${tool name: 'SonarScanner',type:'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner"        
        }      
      }    
    }*/    

    
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
