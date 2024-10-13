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

        stage("build"){
            steps {
                echo " ===> Build started <==="
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo " ===> Build end <==="
            }
        }

        stage("Test"){
            steps {
                echo " ===> unit test started <==="
                sh """ mvn surefire-report:report """
                echo " ===> unit test ended <==="
            }
        }

        stage("SonarQube scan"){

            environment{
                sonarScan = tool 'sonar-scanncer';
            }

            steps {

                sh "java -version"
                
                echo"----------- Halo ----------"

                withSonarQubeEnv('sonar-scanner-server'){
                    withCredentials([string(credentialsId: 'sonar_token', variable: 'SONAR_TOKEN')]){
                        sh """
                            mvn clean verify sonar:sonar \
                            -Dsonar.projectKey=trend-app \
                            -Dsonar.projectName=trend-app \
                            -Dsonar.host.url=http://178.128.84.214:9002 \
                            -Dsonar.login=${SONAR_TOKEN}
                        """
                    }
                }
                
                // withSonarQubeEnv('sonar-scanner-server'){
                //    sh "${sonarScan}/bin/sonar-scanner"
                // }

            }

        }
        stage("Quality Gate"){

            environment{
                sonarScan = tool 'sonar-scanncer';
            }

            steps {
                script{
                    echo"----------- Quality Gate ----------"
                    timeout(time:1, unit:'HOURS'){
                        def qg = waitForQualityGate()
                        if(qg.status != "OK"){
                            error "Pipeline aborted due to quality gate failur: ${qg.status}"
                        }
                    }
                    
                    echo"----------- Quality Gate End ----------"
                
                }
            }

        }

        



    }    
}
