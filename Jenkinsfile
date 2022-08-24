def project_name = 'inkwiry'
def deploy_path = '/var/www/html/sandboxv2'
def production_branch = ''
def development_branch = 'optimization/remove-assets'
def agentName = ''
def ip_address = ''
def user = ''


if (env.BRANCH_NAME == "${development_branch}")
{
  agentName = 'f9a3609e-df99-4afc-a210-fcf470ac889d'
  ip_address = "3.143.216.0"
  user = "ubuntu"
}
if (env.BRANCH_NAME == "${production_branch}")
{
  deploy_path = "/var/www/html"
}

pipeline {
    agent none
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
                        sh "mv .env.develop .env"
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
