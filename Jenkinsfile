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
    }
}