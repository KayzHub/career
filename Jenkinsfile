pipeline {
    agent any
    environment {
        SSH_CRED = credentials('web-server-key')
        def CONNECT = 'ssh -o StrictHostKeyChecking=no -i $SSH_CRED ec2-54-227-193-51.compute-1.amazonaws.com'
        def WEBSERVER = 'ec2-54-227-193-51.compute-1.amazonaws.com'
    }
    stages {
        
        stage('Build') {
            steps {
                echo 'packaging app'
                sh "ls"
                sh "pwd"
                sh "zip -r webapp.zip ."
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying'
                sshagent(['web-server-key']) {
                    sh 'scp -o StrictHostKeyChecking=no -i $SSH_CRED webapp.zip ubuntu@ec2-54-227-193-51.compute-1.amazonaws.com:/home/ubuntu'
                    sh '$CONNECT -i $SSH_CRED "curl ifconfig.io"'
                    sh '$CONNECT -i $SSH_CRED "sudo apt install zip -y"'
                    sh '$CONNECT -i $SSH_CRED "rm -rf /var/www/html/"'
                    sh '$CONNECT -i $SSH_CRED "mkdir /var/www/html/"'
                    // sh '$CONNE-i $SSH_CRED CT "unzip /home/ubuntu/webapp.zip -d /home/ubuntu/app"'
                    sh '$CONNECT -i $SSH_CRED "unzip webapp.zip -d /var/www/html/"'
                    sh '$CONNECT -i $SSH_CRED "rm /var/www/html/config/connect.php"'
                    sh '$CONNECT -i $SSH_CRED "cp /home/ubuntu/connect.php /var/www/html/config/"'
                    sh '$CONNECT -i $SSH_CRED "sudo sh /var/www/html/database/test-db.sh"'
                    
                    // sh '$CONNECT "cp -r /home/ubuntu/app/. /var/www/html/"'
                    
                }
            }
        }


        stage('Clean-Up') {
            steps {
                echo 'Remove existing files'
                sshagent(['web-server-key']) {
                    sh '$CONNECT "sudo rm /home/ubuntu/webapp.zip"'
                }
                sh "rm webapp.zip"
            }
        }
    }
}
