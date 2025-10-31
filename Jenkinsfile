pipeline {
    agent any
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }
    tools {
     nodejs 'NodeJS'
    }
	
    triggers {
        githubPush()
    }
    environment {
        user ="ec2-user"
        HOST_NAME="52.73.202.229" 
        sshcredentials = "ssh-ngnix"
        nodes_installion_folder ="/home/ec2-user/nodejs"
    }
    stages {
        stage("Install npm"){
            steps{
                sh "npm int -y "
            }
        } 
        stage("install dependences"){
            steps{
                sshagent([env.sshcredentials]) {
                    sh"""
                        ssh -o StrictHostKeyChecking=no ${user}@${HOST_NAME} "mkdir /home/ec2-user/nodejs " || true
                        rsync -avz --exclude=node_modules --exclude=.git ./ ${user}@${HOST_NAME}:${nodes_installion_folder}/

                    """           
                }   

            }
        }
        stage("deploy"){
            steps{
                sshagent([env.sshcredentials]) {
                    sh"""
                        ssh -o StrictHostKeyChecking=no ${user}@${HOST_NAME}  " cd ${nodes_installion_folder} &&
                       
                        npm install &&
                        npx pm2 start app.js --name my-app --update-env || npx pm2 restart my-app 
                        "
                    """           
            }   

            }
        }


    }
}
