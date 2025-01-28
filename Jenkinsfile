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
            sh 'mvn jar:jar install:install'
            sh 'java -jar target/my-app-1.0-SNAPSHOT.jar'
            sleep(60)
            echo 'Deployment Selesai'
        }
    }
}