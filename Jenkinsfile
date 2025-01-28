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
        stage('Manual Approval'){
            echo 'Manual Approval'
            input message: "melanjutkan eksekusi pipeline ke tahap Deploy? (Klik Proceed untuk melanjutkan)"
        }
        stage('Deploy'){
            echo 'Deployment Stage'
            sshagent(credentials: ['deploy-ec2']) {
                sh '''
                    echo "Entering EC2"
                    ssh -o StrictHostKeyChecking=no ubuntu@ec2-13-250-119-151.ap-southeast-1.compute.amazonaws.com << EOF
                    
                    echo "Copy jar file to EC2"
                    scp -o StrictHostKeyChecking=no target/my-app-1.0-SNAPSHOT.jar ubuntu@ec2-13-250-119-151.ap-southeast-1.compute.amazonaws.com:/home/ubuntu
                    
                    echo "Run jar file on EC2"
                    java -jar target/my-app-1.0-SNAPSHOT.jar
EOF
                '''                
            }
            sleep(60)
            echo 'Deployment Success'
        }
    }
}