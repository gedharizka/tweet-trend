pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
}
    stages {
        stage("Clone Code"){
            steps {
                git branch:'dev', url: 'https://github.com/gedharizka/tweet-trend.git'
            }
        }

    }
    stages {
        stage("build"){
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage("SonarQube scan"){

            environment{
                sonarScan = tool 'sonar-scanncer';
            }

            steps {

                withSonarQubeEnv('sonar-scanner-server'){
                    sh """
                        ${sonarScan}/bin/sonar-scanner 
                    """
                }

            }

        }





    }
}
