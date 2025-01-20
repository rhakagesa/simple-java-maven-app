node {
    checkout scm

    docker.image('maven:3.9.2').inside('-u root') {
        stage('Build') {
            sh 'mvn -B -DskipTests clean package'
        }
        stage('Test') {
            sh 'mvn test'            
        }
    }
}