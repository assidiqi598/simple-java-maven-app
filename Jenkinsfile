node {
    properties([
        pipelineTriggers([
            pollSCM('H/2 * * * *')
        ])
    ])

    docker.image('maven:3.9.4').inside('-v ${pwd()}/.m2:/root/.m2') {
        stage('Prepare') {
            sh 'mkdir -p .m2 && chmod -R 777 .m2'
        }
        stage('Checkout') {
            checkout scm
            sh '''
                echo "Current Directory: $(pwd)"
                echo "Workspace Contents:"
                ls -la
            '''
        }
        stage('Build') {
            sh 'mvn -B -DskipTests clean package'
        }
        stage('Test') {
            try {
                sh 'mvn test'
            }
            finally {
                junit 'target/surefire-reports/*.xml'
            }
        }
        stage('Deliver') {
            sh './jenkins/scripts/deliver.sh'
        }
    }
}