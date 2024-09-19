pipeline {
    agent none 
    tools {
        maven 'MAVEN3'
        jdk 'JDK17'
    }
    environment{
        SONAR_TOKEN = credentials('SONARQUBE_TOKEN')
    } 
    def DATACENTER_LIST

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
            post{
                always {
                    junit stdioRetention: '', testResults: '**/target/surefire-reports/*.xml'
                }
                success {
                    // archive jar
                    archiveArtifacts artifacts: 'application/target/*.jar', followSymlinks: false
                    dir('application/target') { 
                        stash includes: '*.jar', name: 'DEPLOY_JAR'
                    }  
                }
                unstable {
                    // send mail
                    mail bcc: '', body: 'your build has failed', cc: '', from: '', replyTo: '', subject: 'Build failed', to: 'olivier.vercaemer@bnpparibas.com'
                }
            } 
        }
        stage('Analyse qualité et vulnérabilités') {
            parallel {
                stage('Vulnérabilités') {
                    agent any
                    steps {
                        echo 'Tests de Vulnérabilités OWASP'
                        //sh './mvnw -DskipTests verify'
                    }
                  
                }
                stage('Analyse Sonar') {
                    agent any
                    steps {
                        echo 'Analyse sonar'
                        //sh './mvnw -Dsonar.login=${SONAR_TOKEN} clean integration-test sonar:sonar'
                    }
                }
            }
            
        }
            
        stage('Déploiement intégration') {
            agent any
            input {
                message 'Dans quel Data Center, voulez-vous déployer l’artefact ?'
                ok 'Déployer'
                parameters {
                    choice choices: ['Paris','Lille','Lyon'], name: 'DATACENTER'
                }
            }

            steps {
                echo "Déploiement intégration"
                unstash 'DEPLOY_JAR'
                sh "cp *.jar /home/plb/mywork/Serveurs/$DATACENTER"
                /*script {
                    def deployementsTargets = readJSON file: 'deployments.json'
                    //assert deployementsTargets['dataCenters'].length() > 0
                    for( datacenter in deployementsTargets['dataCenters'] ) {
                        sh "cp *.jar /home/plb/mywork/Serveurs/$datacenter"
                    }
                } */
            }
        }
    }    
}

