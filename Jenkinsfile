pipeline {
   agent any 


    stages {
        stage('Compile et tests') {
            steps {
                echo 'Unit test et packaging'
                def mvnHome
                tools {
                    maven 'MAVEN3'
                    jdk 'JDK17'
                }
                mvnHome = tool 'MAVEN3'
                // Run the maven build
                withEnv(["MVN_HOME=$mvnHome"]) {
                    if (isUnix()) {
                        sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
                    } else {
                        bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
                    }
                }          
            }
             
        }
        stage('Analyse qualité et vulnérabilités') {
            parallel {
                stage('Vulnérabilités') {
                    steps {
                        echo 'Tests de Vulnérabilités OWASP'
                    }
                    
                }
                 stage('Analyse Sonar') {
                     steps {
                        echo 'Analyse sonar'
                     }
                    
                }
            }
            
        }
            
        stage('Déploiement intégration') {

            steps {
                echo "Déploiement intégration"
                
            }
        }

     }
    
}

