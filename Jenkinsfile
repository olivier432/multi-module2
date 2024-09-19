pipeline {
    agent none 
    tools {
        maven 'MAVEN3'
        jdk 'JDK17'
    }
    environment{
        SONAR_TOKEN = credentials('SONARQUBE_TOKEN')
    } 
    
    stages {
        stage('Compile et tests') {
            agent any
            steps {
                echo 'Unit test et packaging'
                //def mvnHome

                //mvnHome = tool 'MAVEN3'
                // Run the maven build
                sh 'mvn -Dmaven.test.failure.ignore clean package'
            }
        }
        stage('Analyse qualité et vulnérabilités') {
            agent any
            parallel {
                /*stage('Vulnérabilités') {
                    steps {
                        echo 'Tests de Vulnérabilités OWASP'
                        sh './mvnw -DskipTests verify'
                    }
                  
                }*/
                stage('Analyse Sonar') {
                    steps {
                        echo 'Analyse sonar'
                        sh './mvnw -Dsonar.login=${SONAR_TOKEN} clean integration-test sonar:sonar'
                    }
                }
            }
            
        }
            
        stage('Déploiement intégration') {
            input {
                message 'Dans quel Data Center, voulez-vous déployer l’artefact ?'
                ok 'Déployer'
                parameters {
                    choice choices: ['Paris,Lille,Lyon'], name: 'DATACENTER'
                }
            }

            steps {
                echo "Déploiement intégration"
                
            }
        }

    }
    post{
        always {
            junit stdioRetention: '', testResults: '**/target/surefire-reports/*.xml'
        }
        success {
            // archive jar
            archiveArtifacts artifacts: '**/target/*.jar', followSymlinks: false
        }
        unstable {
            // send mail
            mail bcc: '', body: 'your build has failed', cc: '', from: '', replyTo: '', subject: 'Build failed', to: 'olivier.vercaemer@bnpparibas.com'
        }
    } 
    
}

