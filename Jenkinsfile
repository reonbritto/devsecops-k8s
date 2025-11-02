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
    }
}
}