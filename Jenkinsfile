stage 'Download'
    node {
        echo 'Building.....'
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/urwithrajesh/testing-pipeline']]])
        }

stage 'SonarQube'
    node {
        echo 'Testing...'
        withSonarQubeEnv('SonarQube') {
          sh ' /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner -Dsonar.projectBaseDir=/var/lib/jenkins/workspace/CICD-Demo'
            }
    }

stage 'Junit'
  node {
    echo 'Starting Junit Testing'
  }

stage 'Build'
  node {
    echo 'Building Application'
    nodejs('NodeJS') 
      {
        sh '''
          npm install
          npm install junit
          npm start &
        '''
  }
}

stage('Approval'){
    input "Deploy to prod?"
}
stage 'Deploy'
    node {
    echo 'Deploying to server..'
    sh '''rsync -auv /var/lib/jenkins/workspace/CICD-Demo/* /appl/node/
        /var/lib/jenkins/.nvm/versions/node/v8.5.0/bin/forever start -al /appl/logs/app.log -c /var/lib/jenkins/.nvm/versions/node/v8.5.0/bin/node /appl/node/index.js'''    
    }
stage 'Notification'
    node {
        def summary = " Running Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]. Check Status at (${env.BUILD_URL}console)' "
         // Send slack notifications all messages
        slackSend baseUrl: 'https://utdigital.slack.com/services/hooks/jenkins-ci/', channel: 'chatops', message: summary , teamDomain: 'utdigital', token: 'a8p3yJ8BdYURLzmorsUyaIaI'
    }
