pipeline {
    agent none
    environment {
        MAJOR_RELEASE= 1
    }
    stages {
        stage('Unit Testing') {
            agent {
                label javaUnitTest
            }
            steps {
                sh 'ant -f test.xml -v'
            }
        }
        stage('Build') {
            agent {
                label masternode
            }
            steps {
                sh 'ant -f Build.xml -v'
                junit 'reports/result.xml'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
                }
            }
        }
        stage('Deploy') {
            agent {
                label masternode
            }
            steps {
               sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
               sh "cp dist/rectangle_${env.MAJOR_RELEASE}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
            }
        }
        stage('Running on Docker Slave') {
            agent {
                label jdkdocker
            }
            steps {
                sh "wget http://sumitsalunke1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_RELEASE}.${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.MAJOR_RELEASE}.${env.BUILD_NUMBER}.jar 3 4"
            }
        }
    }
}
