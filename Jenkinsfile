pipeline{
    agent any
    tools {
        maven 'mymaven' 
    }
    stages{
        /*stage('checkout')
        {
            steps{
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '89958fc6-2628-4c6b-af3e-d4c4c7921efb', url: 'https://github.com/papun12345/RIG_RULE_ENGINE.git']]])checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/papun12345/RIG_RULE_ENGINE.git']]])
            }
        }*/
        stage ('build and install'){
            steps{
                sh "mvn clean install"
            }
        }
      /*  stage('Sonar') {
            environment {
                scannerHome=tool 'sonar scanner'
            }
            steps{
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'Hemant_Sonar_Cred', usernameVariable: 'USER', passwordVariable: 'PASS']]){
                    sh "mvn $USER:$PASS -Dsonar.host.url=http://ec2-18-224-155-110.us-east-2.compute.amazonaws.com:9000"
                }
            }
        }*/
        stage ('Uploading artifact to nexus'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'Hemant_Nexus_Cred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh label: '', script: 'curl -u $USER:$PASS --upload-file target/RulesGamification-0.0.1-SNAPSHOT.war http://ec2-18-224-155-110.us-east-2.compute.amazonaws.com:8081/nexus/content/repositories/devopstraining/TW_Backend/RulesGamification-0.0.1-SNAPSHOT.war'
                }
            }
        }
        stage ('Deploy'){
            steps{
                
                withCredentials([usernamePassword(credentialsId: 'tomcat_ashish', passwordVariable: 'pass', usernameVariable: 'userId')])
                  {
                              sh 'curl -u  $userId:$pass  http://ec2-13-233-251-211.ap-south-1.compute.amazonaws.com:8080/manager/text/undeploy?path=/TW_Backend_2'
                 sh  'curl -u  $userId:$pass --upload-file target/RulesGamification-0.0.1-SNAPSHOT.war  http://ec2-13-233-251-211.ap-south-1.compute.amazonaws.com:8080/manager/text/deploy?config=file:/var/lib/tomcat8/RulesGamification-0.0.1-SNAPSHOT.war\\&path=/TW_Backend_2'
                     echo "war"
                 }
                /*withCredentials([usernamePassword(credentialsId: 'Hemant_Nexus_Cred', passwordVariable: 'PASS1', usernameVariable: 'USER1'),
                                 usernamePassword(credentialsId: 'tw-tomcat-manager', passwordVariable: 'PASS2', usernameVariable: 'USER2')]) {
                    //sh label: '', script: 'curl -u $USER2:$PASS2 http://ec2-52-66-189-143.ap-south-1.compute.amazonaws.com:8080/manager/text/undeploy?path=/TW_Bachend_1'
                    //sh label: '', script: 'curl -u  $USER2:$PASS2 --upload-file target/RulesGamification-0.0.1-SNAPSHOT.war http://ec2-52-66-189-143.ap-south-1.compute.amazonaws.com:8080/manager/text/deploy?config=file:/opt/tomcat/webapps/RulesGamification-0.0.1-SNAPSHOT.war\\&path=/TW_Backend_2'
                     sh label: '', script: 'curl -u  $USER2:$PASS2  http://ec2-52-66-189-143.ap-south-1.compute.amazonaws.com:8080/manager/text/deploy?config=file:/opt/tomcat/webapps/myWebApp_Test-0.0.1-SNAPSHOT.war\\&path=/TW_Backend_3'
                    //sh label: '', script: 'curl -u $USER1:$PASS1  "http://3.14.251.87:8081/nexus/content/repositories/devopstraining/Hemant/redirect?r=releases&g=com.wipro.springboot&a=loginpage&v=${BUILD_NUMBER}&e=war" -o -u $USER2:$PASS2 http://ec2-18-224-182-74.us-east-2.compute.amazonaws.com:8080/manager/text/deploy?config=file:/var/lib/tomcat8/loginpage-${BUILD_NUMBER}.war\\&path=/login_hemant'
                }*/
            }
        }
    }
    post {
       success {
           slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       }
       failure {
           slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       }
    }
}
