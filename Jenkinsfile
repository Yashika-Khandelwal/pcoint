def COLOR_MAP = ['SUCCESS': 'good', 
    'FAILURE': 'danger',
    ]
def getBuildUser(){
    return currentBuild.rawBuild.getCause(Cause.UserIdCause).getUserId()}
pipeline {
    // Set up local variables for your pipeline
    environment {
        // test variable: 0=success, 1=fail; must be string
        doError = '0'
        BUILD_USER = ''
    }

    agent any

    stages {
        stage('Error') {
            // when doError is equal to 1, return an error
            when {
                expression { doError == '1' }
            }
            steps {
                echo "Failure :("
                error "Test failed on purpose, doError == str(1)"
            }
        }
        stage('Success') {
            // when doError is equal to 0, just print a simple message
            when {
                expression { doError == '0' }
            }
            steps {
                 sh( returnStdout: true, script: '''
                sudo rm -r /Desktop/pcoint
                sudo git clone https://github.com/Yashika-Khandelwal/pcoint.git /Desktop/pcoint

                if sudo docker ps -a | grep pcoint
                then
                sudo docker rm -f pcoint
                sudo docker run -d -p 8081:80 -v /pcoint:/usr/local/apache2/htdocs --name pcoint httpd
                else
                sudo docker run -d -p 8081:80 -v /pcoint:/usr/local/apache2/htdocs --name pcoint httpd
                fi
                '''.stripIndent())
                echo "Success :)"
            }
        }
    }

    // Post-build actions
    post {
        always {
            script {
                BUILD_USER = getBuildUser()
            }
            echo 'I will always say hello in the console.'
            slackSend channel: '#slack-jenkins',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} by ${BUILD_USER}\n Commit id '${env.GIT_COMMIT}' \n More info at: ${env.BUILD_URL}"
        }
        
    }
}
