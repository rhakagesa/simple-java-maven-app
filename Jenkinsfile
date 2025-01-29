node {
    checkout scm

    docker.image('maven:3.9.2').inside('-u root') {
        stage('Build') {
            echo 'Build'
            sh 'mvn -B -DskipTests clean package'
        }
        stage('Test') {
            echo 'Test'
            sh 'mvn test'
        }
        stage('Manual Approval') {
            echo 'Manual Approval'
            input message: "Lanjutkan ke tahap Deploy?"
        }
        stage('Deploy') {
            echo 'Deployment Stage'
            withCredentials([sshUserPrivateKey(credentialsId: 'deploy-ec2', keyFileVariable: 'SSH_PRIVATE_KEY', usernameVariable: 'SSH_USER', passphraseVariable: 'PUBLIC_DNS')]) {
                sh '''
                    echo "Install openssh client"
                    apt-get update && apt-get install -y openssh-client

                    echo "Entering EC2"
                    scp -o StrictHostKeyChecking=no -i $SSH_PRIVATE_KEY target/my-app-1.0-SNAPSHOT.jar ${SSH_USER}@${PUBLIC_DNS}:/home/ubuntu
                    
                    echo "Run jar file on EC2"
                    ssh -o StrictHostKeyChecking=no -i $SSH_PRIVATE_KEY ${SSH_USER}@${PUBLIC_DNS} << EOF
                        java -jar /home/ubuntu/my-app-1.0-SNAPSHOT.jar
EOF
                '''
            }

            sleep(60)
            echo 'Deployment Success'
            echo 'Removing JAR file'
            withCredentials([sshUserPrivateKey(credentialsId: 'deploy-ec2', keyFileVariable: 'SSH_PRIVATE_KEY')]) {
                sh '''
                    ssh -o StrictHostKeyChecking=no -i $SSH_PRIVATE_KEY ubuntu@ec2-13-250-119-151.ap-southeast-1.compute.amazonaws.com << EOF
                        rm /home/ubuntu/my-app-1.0-SNAPSHOT.jar
EOF
                '''
            }
            echo 'JAR file removed'
        }
        
    }
}
