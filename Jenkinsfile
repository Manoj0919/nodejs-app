pipeline {
    agent any
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }
	
    triggers {
        githubPush()
    }
    environment {
        user ="ec2-user"
        HOST-NAME="54.174.158.42" 
        sshcredentials = "ssh-ngnix"
        nodes-installion-folder ="/home/ec2-user/nodejs"
    }
    tools {
        nodejs 'NodeJS'
    }
    stages {
        stage("Install npm"){
            steps{
                sh "npm install"
            }
        }
        stage("install dependences"){
            steps{
                sshagent([env.sshcredentials]) {
                    sh"""
                        ssh -o StrictHostKeyChecking=no ${user}@${RHOST-NAME} "mkdir /home/ec2-user/nodejs " || true
                        rsync -avz --exclude=node_modules --exclude=.git ./ ${user}@${RHOST-NAME}:${nodes-installion-folder}/
                    """           
                }   

            }
        }
        stage("deploy"){
            steps{
                sshagent([env.sshcredentials]) {
                    sh"""
                        ssh -o StrictHostKeyChecking=no ${user}@${RHOST-NAME}  cd ${nodes-installion-folder} &&
                       
                        npm install &&
                        npx pm2 start app.js --name my-app --update-env || npx pm2 restart my-app
                    """           
            }   

            }
        }


    }
}
