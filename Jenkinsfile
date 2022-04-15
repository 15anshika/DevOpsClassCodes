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
                sh 'mvn -P metrics pmd:pmd' 
            }
            post {
                success {
					// pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: 'target/pmd.xml', unHealthy: ''
                    //publishIssues issues: [pmd]
					//scanForIssues tool: pmd(pattern: '**/target/pmd.xml')
					recordIssues(
						aggregatingResults: true, enabledForFailure: true, ignoreQualityGate: true, sourceCodeEncoding: 'UTF-8', sourceDirectory: 'src/main/java',
						tools: [
						//checkStyle(pattern: 'target/checkstyle-result.xml', reportEncoding: 'UTF-8'),
						//mavenConsole(),
						//java(),
						//cpd(pattern: 'target/cpd.xml', reportEncoding: 'UTF-8'),
						pmdParser(pattern: 'target/pmd.xml', reportEncoding: 'UTF-8'),
						//spotBugs(pattern: 'target/spotbugsXml.xml', reportEncoding: 'UTF-8', useRankAsPriority: true)
            ]
        )
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



