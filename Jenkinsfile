def project_name = 'inkwiry'
def production_branch = ''
def development_branch = 'optimization/remove-assets'

if (env.BRANCH_NAME == "${development_branch}")
{
  agentName = 'dev'
  ip_address = '3.15.103.154'
  user = 'ubuntu'
  deploy_path = '/var/www/html/inkwiry-develop'
}
if (env.BRANCH_NAME == "${production_branch}")
{
  deploy_path = ''
  agentName = ''
  ip_address = ''
  user = ''
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
                script {
                        sh "npm install"
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
                            sh "apt update && apt -y install rsync"
                            sh "rsync -avrHP -e 'ssh -o StrictHostKeyChecking=no' --delete build/ ${user}@${ip_address}:${deploy_path}"
                            sh "docker system prune -f"
                        }
                    }
                    else if (env.BRANCH_NAME == "${production_branch}" && env.NODE_LABEL != "master" )
                    {   
                        sshagent ( ["${agentName}"]) {
                            sh "ls build"
                            sh "apt update && apt -y install rsync"
                            sh "rsync -avrHP -e 'ssh -o StrictHostKeyChecking=no' --delete build/ ${user}@${ip_address}:${deploy_path}"
                            sh "docker system prune -f"
                        }
                    }
                }
            }
        }
    }
}
