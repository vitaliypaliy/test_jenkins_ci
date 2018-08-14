pipeline {
    agent {
        docker { 
            image 'node:8.11.3-jessie' 
            args '-u 0:0'
        }
    }
    stages {
        stage('Merge with prod org'){
            steps {
                sh 'node --version'
                sh 'npm install --global force-dev-tool --silent'
                withCredentials([usernamePassword(credentialsId: 'SFCredantials', passwordVariable: 'SF_PASSWORD', usernameVariable: 'SF_USERNAME')]) {
                    sh 'force-dev-tool remote add production ${SF_USERNAME} ${SF_PASSWORD} https://login.salesforce.com' 
                }
                sh 'git checkout -b deploy'
                sh 'rm -rf src/'
                sh 'force-dev-tool fetch --progress production'
                sh 'force-dev-tool package -a production'
                sh 'force-dev-tool retrieve production'
                sh 'git add .'
                sh 'git config --global user.email "vitaliy.paliy@ctdev.io"' 
                sh 'git config --global user.name "Vitaliy Paliy"'  
                sh 'git commit -m "deploy_commit"'
                sh 'git checkout -b org deploy'
                sh 'git checkout deploy'
                sh 'cat src/objects/TestObject__c.object'
                sh 'git merge origin/master'
                sh 'git checkout master'
                sh 'cat src/objects/TestObject__c.object'
            }
        }
        stage('Running tests & dry-run deploy'){
            steps {
                withCredentials([usernamePassword(credentialsId: '7c84d7d6-f409-4ab3-9a5d-001e6d3d0606', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh 'git remote set-url origin https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/test_jenkins_ci.git'
                    sh 'git push origin master'
                }
                sh 'git diff --no-renames --name-only org deploy | tr \'\\n\' \' \''
                sh 'force-dev-tool changeset create deploy $(git diff --no-renames --name-only org deploy | tr \'\\n\' \' \')'
                sh 'force-dev-tool deploy -ct  -d config/deployments/deploy production'
            }
        }
        stage('Deploy'){
            steps {
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
            sh 'rm -rf Jenkinsfile_scheduled'
            deleteDir()

        }
    }
}