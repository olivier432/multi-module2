pipeline {
    agent any 
    tools {
        maven 'MAVEN3'
        jdk 'JDK17'
    }

    stages {
        stage('Compile et tests') {
            steps {
                echo 'Unit test et packaging'
                //def mvnHome

                //mvnHome = tool 'MAVEN3'
                // Run the maven build
                sh 'mvn -Dmaven.test.failure.ignore clean package'
                always {
                    junit stdioRetention: '', testResults: '**/target/surefire-reports/*.xml'
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

