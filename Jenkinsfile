pipeline {
    agent any
    tools {
        maven 'mymaven'
        jdk 'java'
    }
    stages {
        stage ('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage ('CodeReview') {
            steps {
                sh 'mvn -Pmetrics pmd:pmd' 
            }
            post {
                success {
					pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: 'target/pmd.xml', unHealthy: ''
                    
                }
            }
        }
		stage ('QAUnitTest') {
            steps {
                sh 'mvn test' 
            }
            post {
                success {
					junit 'target/surefire-reports/*.xml'
                    
                }
            }
        }
		stage ('QAMetricCheck') {
            steps {
                sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml' 
            }
            post {
                success {
					cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
                    
                }
            }
        }
		stage ('Package') {
            steps {
                sh 'mvn package' 
            }
            post {
                success {
					emailext body: '''$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:
					Check console output at $BUILD_URL to view the results.''', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'bhatnagarshivam26@gmail.com'
                    
                }
            }
        }
    }
}



