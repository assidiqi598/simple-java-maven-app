node {
    properties([
        pipelineTriggers([
            pollSCM('H/2 * * * *')
        ])
    ])

    docker.image('maven:3.9.0').inside('-v /root/.m2:/root/.m2') {
        stage('Build') {
            try {
                sh 'mvn -B -DskipTests clean package'
            }
            catch (e) {
                echo "Build failed: ${e.getMessage()}"
            }
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
            try {
                sh './jenkins/scripts/deliver.sh'
            }
            catch (e) {
                echo "Build failed: ${e.getMessage()}"
            }
        }
    }
}