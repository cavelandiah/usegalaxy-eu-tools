pipeline {
  agent {
    docker {
      image 'python:2.7'
      args '-u root'
    }
  }
  stages {
    stage('Linting') {
      steps {
        sh 'pip install -r requirements.txt'
        sh 'for i in *.yaml; do pykwalify -d $i -s .schema.yaml; done'
      }
    }
    stage('Updated Trusted Repositories') {
      steps {
        sh '''
        git checkout master
        '''

        sh '''
        pip install -r requirements.txt
        '''
        sh '''
        python scripts/update-trusted.py
        '''

        sshagent(['501e17be-bcda-4159-8cc3-eae39c4797f5']) {
            sh 'git add *.lock'
            sh 'git config --global user.email "jenkins@usegalaxy.eu"'
            sh 'git config --global user.name "usegalaxy.eu jenkins bot"'
            sh 'git commit -m "Updated trusted tools" ||  true'
            sh 'git push origin master || true'
        }
      }
    }

  }
}