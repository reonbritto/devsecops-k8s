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
        stage('mutation testing'){
            steps{
                sh 'mvn org.pitest:pitest-maven:mutationCoverage'
            }
            post{
                always{
                    pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
            }
        }
        }

        stage('SonarQube - SAST') {
           steps {
            withSonarQubeEnv('sonarqube') {
                sh "mvn sonar:sonar -Dsonar.projectKey=numeric-app -Dsonar.projectName='numeric-app'"            
            }
            timeout(time: 2, unit: 'MINUTES') {
                script {
                    waitForQualityGate abortPipeline: true
                }
           }
        }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/']) {
                        sh 'printenv'
                        sh 'docker build -t reonbritto/numeric-app:${GIT_COMMIT} .'
                        sh 'docker push reonbritto/numeric-app:${GIT_COMMIT}'
                    }
                }
            }
        }
        stage('Deploy to Kubernetes - dev'){
            steps{
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh "sed -i 's#replace#reonbritto/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                    sh "kubectl apply -f k8s_deployment_service.yaml"
                }
            }
        }
        
    }
}

