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
            input message: "Melanjutkan eksekusi pipeline ke tahap Deploy? (Klik Proceed untuk melanjutkan)"
        }
        stage('Deploy') {
            echo 'Deployment Stage'
            withCredentials([sshUserPrivateKey(credentialsId: 'deploy-ec2', keyFileVariable: 'SSH_PRIVATE_KEY')]) {
                sh '''
                    echo "Install openssh client"
                    apt-get update && apt-get install -y openssh-client

                    echo "Entering EC2"
                    scp -o StrictHostKeyChecking=no -i $SSH_PRIVATE_KEY target/my-app-1.0-SNAPSHOT.jar ubuntu@ec2-13-250-119-151.ap-southeast-1.compute.amazonaws.com:/home/ubuntu
                    
                    echo "Run jar file on EC2"
                    ssh -o StrictHostKeyChecking=no -i $SSH_PRIVATE_KEY ubuntu@ec2-13-250-119-151.ap-southeast-1.compute.amazonaws.com << EOF
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
