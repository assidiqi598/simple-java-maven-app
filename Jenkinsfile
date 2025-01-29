node {
    properties([
        pipelineTriggers([
            pollSCM('H/2 * * * *')
        ])
    ])

    docker.image('maven:3.9.4').inside("--user root -v /root/.m2:/root/.m2") {
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
            input message: 'Lanjutkan ke tahap Deploy?'
        }
        stage('Deploy') {
            def jarFile = "target/my-app-1.0-SNAPSHOT.jar"
            def vpsUser = "ubuntu"
            def vpsHost = "152.69.212.167"
            def vpsPath = "/home/ubuntu"
            sh 'ls target/'
            sh 'apt-get update && apt-get install -y openssh-client'
            withCredentials([sshUserPrivateKey(credentialsId: 'oracle-vps-private-key', keyFileVariable: 'SSH_KEY')]) {
                sh """
                    mkdir -p ~/.ssh
                    ssh-keyscan -H ${vpsHost} >> ~/.ssh/known_hosts
                    chmod 600 ~/.ssh/known_hosts
                """
                sh """
                    scp -i $SSH_KEY ${jarFile} ${vpsUser}@${vpsHost}:${vpsPath}/my-app-1.0-SNAPSHOT.jar
                """
                sh """
                    ssh -i $SSH_KEY ${vpsUser}@${vpsHost} "java -jar ${vpsPath}/my-app-1.0-SNAPSHOT.jar"
                    sleep 60
                """
            }
        }
    }
}