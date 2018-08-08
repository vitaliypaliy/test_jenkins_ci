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
                sh 'force-dev-tool fetch --progress production'
                sh 'force-dev-tool package -a production'
                sh 'force-dev-tool retrieve production'
                sh 'git init'
                sh 'git add .' 
                sh 'git remote add origin https://vitaliy.paliy:080494VpV@gitlab.com/vitaliy.paliy/test.git'
                sh 'git checkout -b deploy'
                sh 'git add .'
                sh 'git config --global user.email "vitaliy.paliy@ctdev.io"' 
                sh 'git config --global user.name "Vitaliy Paliy"' 
                sh 'git config --global user.username "vitaliy.paliy"' 
                sh 'git config --global user.password "080494VpV"' 
                sh 'git fetch --all'
                sh 'git commit -m "deploy_commit"'
                sh 'git push origin deploy'
                sh 'git checkout master'
                sh 'git diff --no-renames --name-only master deploy | tr \'\\n\' \' \''
                sh 'force-dev-tool changeset create deploy $(git diff --no-renames --name-only master deploy | tr \'\\n\' \' \')'
                sh 'rm -rf src/*'
                sh 'cp -a config/deployments/deploy/. src/'
                sh 'force-dev-tool deploy -ct production'
                sh 'force-dev-tool deploy production'
            }
        }
    }
    post{
        always{
            sh 'git push --delete origin deploy'
            deleteDir()
        }
    }
}