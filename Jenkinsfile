pipeline{
    agent any
    stages{
        stage('check out source code'){
            steps{
                git branch: 'main', credentialsId: 'githubtoken', url: 'https://github.com/Siddeshg672/hello_world_public_war.git'
            }
        }
        stage('sonar scan'){
            steps{
                script{
                    def mvn = tool 'maven1';
                    withSonarQubeEnv('SonarScanNew') {
                      sh "mvn clean verify sonar:sonar -Dsonar.projectKey=applicationB -Dsonar.projectName='applicationB'"
                    }
                }
            }
        }
        stage('create binaries'){
            steps{
                sh 'mvn clean install'
            }
        }
        // stage('publish to Jfrog'){
        //     steps{
        //         sh """
        //             curl -usiddeshg672@gmail.com:Siddu@9880 -T '/var/lib/jenkins/workspace/appB/webapp/target/webapp.war' "https://shstechacademynew.jfrog.io/artifactory/pay_war-generic-local/appB/webapp-${BUILD_NUMBER}.war" 
        //         """
        //          // curl -usiddeshg672@gmail.com:cmVmdGtuOjAxOjE3NTkzNzcwMTA6TnZic3gzTmVRaXJoa3FSN05VZnZFVXF4ZWpI -T '/var/lib/jenkins/workspace/appA/webapp/target/webapp.war' "https://shstechacademynew.jfrog.io/artifactory/pay_war-generic-local/appA/webapp-${BUILD_NUMBER}.war"
                
        //     }
        // }
        stage('upload war to jfrog'){
            steps{
                rtUpload (
                    serverId: "Jfrog-dev",                     
                    spec: """{                             
                        "files": [                                     
                                {                                         
                                  "pattern": "/var/lib/jenkins/workspace/appB/webapp/target/webapp.war",                                         
                                  "target": "pay_war-generic-local/appB/webapp-${BUILD_NUMBER}.war"
                                }                                 
                            ]                             
                    }"""                 
                )             
            }
        }
        //Deploy with ssh to tomcat
        // stage('deploy to tomcat'){
        //     steps{
        //         sshagent(credentials: ['test'], ignoreMissing: true) {
        //             sh """
        //                 scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/appB/webapp/target/webapp.war ec2-user@18.222.33.27:/home/ec2-user/apache-tomcat-9.0.95/webapps
        //             """
        //         }
        //     }
        // }

        //Deploy with ansible to tomcat
        stage('deploy with ansible'){
            steps{
                sh """
                        chmod 400 terraform_ohio.pem
                        ansible-playbook tomcatDeploy.yml -i hosts.ini --private-key terraform_ohio.pem -u ec2-user -e "BUILD_NUMBER=${ BUILD_NUMBER }"
                    """
            }
        }
    }
}
