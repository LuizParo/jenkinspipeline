pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '54.227.216.41', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '52.207.234.227', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') 
    }
    
    tools {
        maven 'localMaven'
    }

stages{
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
    
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "pwd && scp -i /Users/Shared/Jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "pwd && scp -i /Users/Shared/Jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}
