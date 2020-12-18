pipeline {  

  // sample values commented below are for https://github.com/feedhenry-templates/helloworld-ios-swift
  /* ------------- use these to hardcode values in Jenkinsfile ---------------- */
  PROJECT_NAME = "ICTest"
  INFO_PLIST = "ICTest/Info.plist"
  VERSION = "1.0.0"
  SHORT_VERSION = "1.0"
  BUNDLE_ID = "co.com.ceiba.ICTest"
  OUTPUT_FILE_NAME="${PROJECT_NAME}-${BUILD_CONFIG}.ipa"
  SDK = "iphoneos"

  XC_VERSION = ""                       // use something like 8.3 to use a specific XCode version.
                                        // If this is not set, the default Xcode on the machine will be used

  CLEAN = true                          // do a clean build and sign
  
  FH_CONFIG_CONTENT = params?.FH_CONFIG_CONTENT ?: ""

  // RHMAP specific values. RHMAP currently sends build config type all lower case. we handle it here as case matters for Xcode.
  // also, RHMAP can send "Distribution" config. we use "Release" in that case.
  if(BUILD_CONFIG.toLowerCase() == "debug"){
      BUILD_CONFIG = "Debug"
  }
  else if(BUILD_CONFIG.toLowerCase() == "release" || BUILD_CONFIG.toLowerCase() == "distribution"){
      BUILD_CONFIG = "Release"
  }
  
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
  //tools {    
  //  jdk 'JDK8_Centos' 
    //Preinstalada en la Configuración del Master    
  //  gradle 'Gradle4.5_Centos' 
    //Preinstalada en la Configuración del Master  
  //}

  //Aquí comienzan los “items” del Pipeline  
  node('ios') { 

    stage('Checkout') {      
      steps{        
        echo "------------>Checkout<------------"      
        checkout scm
      }    
    }    
    
    stage('Prepare') {
        echo "------------>Unit Tests<------------"  
        writeFile file: "${PROJECT_NAME}/fhconfig.plist", text: FH_CONFIG_CONTENT
        sh '/usr/local/bin/pod install'
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
        withEnv(["XC_VERSION=${XC_VERSION}"]) {
            xcodeBuild(
                    cleanBeforeBuild: CLEAN,
                    src: './',
                    schema: "${PROJECT_NAME}",
                    workspace: "${PROJECT_NAME}",
                    buildDir: "build",
                    sdk: "${SDK}",
                    version: "${VERSION}",
                    shortVersion: "${SHORT_VERSION}",
                    bundleId: "${BUNDLE_ID}",
                    infoPlistPath: "${INFO_PLIST}",
                    xcodeBuildArgs: 'ENABLE_BITCODE=NO OTHER_CFLAGS="-fstack-protector -fstack-protector-all"',
                    autoSign: false,
                    config: "${BUILD_CONFIG}"
            )
        }
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
