pipeline {
    agent { label 'jenkinsslave' }

    environment {
        DEPLOY_IP = '172.26.240.11'   // Replace with your actual WSL IP
    }

    stages {
        stage('Clone') {
            steps {
                git url: 'https://github.com/amreshsharma199/flaskapp.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build --no-cache -t flask-jenkins-app  .'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'docker run --rm flask-jenkins-app python3 test_app.py'
            }
        }

        // stage('Deploy to WSL') {
        //     steps {
        //         echo "Deploying to WSL..."
        //         sh """
        //         ssh -o StrictHostKeyChecking=no amresh@${DEPLOY_IP} 'docker rm -f myapp || true'
        //         ssh amresh@${DEPLOY_IP} 'docker run -d --name myapp -p 5000:5000 flask-jenkins-app'
        //         """
        //     }
        // }
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
