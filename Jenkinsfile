pipeline{
    agent any

    stages{
        stage('Build artifact'){
            steps{
                sh 'mvn clean package -DskipTests=true'
                archive 'target/*.jar' // Archive the built JAR files
            }
        }
        stage('test'){
            steps{
                sh 'mvn test'
            }
            post{
                always{
                    junit 'target/surefire-reports/*.xml'
                    jacoco execPattern: 'target/jacoco.exec'
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    sh 'printenv'
                    sh 'docker build -t reonbrito/numeric-app:""${GIT_COMMIT}"" .'
                    sh 'docker push reonbrito/numeric-app:""${GIT_COMMIT}"" '
                }
            }
        }
    }
}
}