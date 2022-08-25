def project_name = 'inkwiry'
def deploy_path = '/var/www/html/inkwiry-develop'
def production_branch = ''
def development_branch = 'master'
def agentName = ''
def ip_address = ''
def user = ''


if (env.BRANCH_NAME == "${development_branch}")
{
  agentName = '2440e3e5-5a5f-47f4-8df9-4a96e7dda2cf'
  ip_address = "3.15.103.154"
  user = "ubuntu"
}
if (env.BRANCH_NAME == "${production_branch}")
{
  deploy_path = '/var/www/html/inkwiry-develop'
  agentName = '2440e3e5-5a5f-47f4-8df9-4a96e7dda2cf'
  ip_address = "3.15.103.154"
  user = "ubuntu"
}

pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: "7"))
    }
    
    stages {
        
        stage("Build")
        {
                agent {
                        docker {
                            image "node:14"
                        }
                    }
            steps
            {   
                sh "npm install"
                script {
                        
                        sh "npm run build"
                        stash includes: 'build/**/*', name: 'BUILD'
                }
            }
        }

        stage("Deploy")
        {
            steps
            {
                script {
                    unstash 'BUILD'
                    if (env.BRANCH_NAME == "${development_branch}" && env.NODE_LABEL != "master")
                    {
                        sshagent ( ["${agentName}"]) {
                           sh "ls build"   
                           sh "rsync -avrHPn -e 'ssh -o StrictHostKeyChecking=no' build/ ${user}@${ip_address}:${deploy_path}"
                           sh "docker system prune -f"
                        }
                    }
                    else if (env.BRANCH_NAME == "${production_branch}" && env.NODE_LABEL != "master" )
                    {   
                        sshagent ( ["${agentName}"]) {
                            sh "ls build"
                            sh "rsync -avrHP -e 'ssh -o StrictHostKeyChecking=no' --delete build/ ${user}@${ip_address}:${deploy_path}"
                            sh "docker system prune -f"
                        }
                    }
                }
            }
        }
    }
}
