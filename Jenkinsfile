pipeline {
    agent {
        docker { 
            image 'node:8.11.3-jessie' 
            args '-u 0:0'
        }
    }
    stages {
        stage('Test') {
            steps {
                sh 'node --version'
                sh 'npm install --global force-dev-tool --silent'
                sh 'force-dev-tool remote add production vitaliy.paliy@ctdev.io 080494VpV https://login.salesforce.com' 
                sh 'git checkout -b deploy'
                sh 'rm -rf *'
                sh 'force-dev-tool fetch --progress production'
                sh 'force-dev-tool package -a production'
                sh 'force-dev-tool retrieve production'
                sh 'git add .'
                sh 'git commit -m "deploy_commit"'
                sh 'git checkout master'
                sh 'git diff --no-renames --name-only master deploy | tr \'\\n\' \' \''
                sh 'force-dev-tool changeset create deploy $(git diff --no-renames --name-only master deploy | tr \'\\n\' \' \')'
                sh 'force-dev-tool deploy -ct  -d config/deployments/deploy production'
                sh 'force-dev-tool deploy -d config/deployments/deploy production'
            }
        }
    }
    post{
        always{
            sh 'rm -rf .git'
            sh 'rm -rf config'
            sh 'rm -rf src'
            sh 'rm -rf Jenkinsfile'
            deleteDir()
        }
    }
}