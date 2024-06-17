pipeline {

    agent {
        label 'amazonvm_slave'
       }
    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }
    environment {
        ScannerHome = tool 'sonar-server'
    }

    stages {
        stage('GIT Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/9030319796/Hello-world.git'
            }
        }
        
        stage('Compile and Clean stage') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                     sh '''
                      $ScannerHome/bin/sonar-scanner -Dsonar.projectName=CICD-project1 \
                      -Dsonar.projectKey=CICD-project1 \
                      -X
                     '''
                   }
            }

        }

        stage('Qulity gate') {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
            }
        }

        stage('OSWAP DP Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'owasp-dp-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
}

}