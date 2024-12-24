pipeline {
   agent {
        label '!windows'
    }

    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }
    stages {
        stage('build') {
           steps {
                sh 'mvn --version'
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
                retry(1) {
                    sleep 5'
                }
           }
        }
         stage('check') {
            steps {
                echo "Database engine is ${DB_ENGINE}"
                echo "DISABLE_AUTH is ${DISABLE_AUTH}"
                sh 'printenv'

                sh './gradlew build'

                input "Does the staging environment look ok?"
           }
         }
         stage('Test') {
            steps {
                sh './gradlew check'
            }
         }   
    }
        post {
        always {
            echo 'This will always run'
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
            junit 'build/reports/**/*.xml'
            echo 'One way or another, I have finished'
            deleteDir()
        }
        success {
            echo 'This will run only if successful'
            echo 'I succeeded!'
            slackSend channel: '#ops-room',
                  color: 'good',
                  message: "The pipeline ${currentBuild.fullDisplayName} completed successfully."
        }
        failure {
            echo 'This will run only if failed'
            echo 'I failed :('
             mail to: 'ihor.taranenko@squad.tech',
             subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
             body: "Something is wrong with ${env.BUILD_URL}"
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
            echo 'I am unstable :/'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
            echo 'Things were different before...'
        }
   }
}
