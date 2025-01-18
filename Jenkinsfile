node {
    properties([
        pipelineTriggers([
            pollSCM('H/2 * * * *')
        ])
    ])
    
    def workspace = pwd()
    docker.image('maven:3.9.4').inside("-v ${workspace}/.m2:/tmp/.m2") {
        stage('Checkout') {
            checkout scm
            sh '''
                echo "Current Directory: $(pwd)"
                echo "Workspace Contents:"
                ls -la
            '''
        }
        stage('Debug') {
            sh '''
                echo "Current User: $(whoami)"
                ls -la /tmp/.m2
            '''
        }
        stage('Build') {
            sh 'mvn -B -Dmaven.repo.local=/tmp/.m2/repository -DskipTests clean package'
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