pipeline {
  agent { label 'jenkinsslave' }

  stages {
    stage('Clone') {
      steps {
        git url: 'https://github.com/amreshsharma199/flaskapp.git'
      }
    }

    stage('Test') {
      steps {
        sh 'docker run --rm -v $PWD:/app -w /app python:3.9-slim bash -c "pip install -r requirements.txt && pytest test_app.py"'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t flask-jenkins-app .'
      }
    }

    stage('Deploy to WSL') {
      steps {
        sshagent(credentials: ['jenkins-agent-key']) {
          sh '''
          docker save flask-jenkins-app | bzip2 | ssh -o StrictHostKeyChecking=no jenkins@172.26.240.11 'bunzip2 | docker load'
          ssh jenkins@172.26.240.11 "docker rm -f myapp || true && docker run -d -p 5000:5000 flask-jenkins-app"
          '''
        }
      }
    }
  }
}


// pipeline {
//     agent { label 'jenkinsslave' }

//     stages {
//         stage('Clone') {
//             steps {
//                 git url: 'https://github.com/amreshsharma199/flaskapp.git', branch: 'main'
//             }
//         }

//         stage('Test') {
//             steps {
//                 sh 'docker run --rm -v $PWD:/app -w /app python:3.9-slim bash -c "pip install -r requirements.txt && pytest test_app.py"'
//             }
//         }

//         stage('Build Docker Image') {
//             steps {
//                 // sh 'docker build -t flask-jenkins-app .'
//             }
//         }
//        stage('Deploy to WSL') {
//            steps {
//              sshagent(['wsl-ssh-key']) {
//                  sh '''
//                     ssh jenkins@172.26.240.11 "docker rm -f myapp || true && docker run -d -p 5000:5000 flask-jenkins-app"

//                 '''
//     }
//   }
// }

//         // stage('Run App') {
//         //     steps {
//         //         sh 'docker run -d -p 5000:5000 --name flaskapp flask-jenkins-app'
//         //     }
//         // }
//     }

//     post {
//         always {
//             echo 'Pipeline complete'
//         }
//     }
// }
