node {
    properties([
        pipelineTriggers([
            pollSCM('H/2 * * * *')
        ])
    ])

    docker.image('maven:3.9.4').inside('-v /root/.m2:/root/.m2') {
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
                echo "Checking /root/.m2 inside container..."
                ls -la /root/.m2
                touch /root/.m2/test_file
                echo "Test file created successfully in /root/.m2"
            '''
        }
        stage('Build') {
            sh 'mvn -B -DskipTests -Dmaven.repo.local=/.m2/repository clean package'
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