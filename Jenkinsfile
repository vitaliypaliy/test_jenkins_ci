pipeline {
    agent {
        docker { image 'node:8.11.3-jessie' }
    }
    stages {
        stage('Test') {
            steps {
                sh node -v
  				sh npm install --global force-dev-tool --silent
  				sh mkdir -p /sf-project && cd /sf-project
  				sh force-dev-tool remote add production vitaliy.paliy@ctdev.io 080494VpV https://login.salesforce.com 
  				sh force-dev-tool fetch --progress production
  				sh force-dev-tool package -a production
  				sh force-dev-tool retrieve production
            }
        }
    }
}