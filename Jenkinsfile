pipeline {
   agent any
   tools {
       maven 'mvn3.6.1'

   }
   stages {
       stage('SCM') {
           steps {
               git branch: 'pmd', url: 'https://github.com/ColmCharlton/CamundaSimpleDemo'
           }
       }
       stage('Build and package maven project') {
           steps {
//               withMaven(maven: 'mvn3.6.1') {
                   sh 'mvn clean package'

               }
           }
       }

       stage('Static code analysis, PMD ') {
           steps {
//               withMaven(maven: 'mvn3.6.1') {
                   sh 'mvn jxr:jxr pmd:cpd'
                   sh 'mvn jxr:jxr pmd:pmd'

               }
           }
//       }
//       stage('Static code analysis, SonarQube ') {
//           steps {
//               withSonarQubeEnv('sonarqube') {
//                   // Optionally use a Maven environment you've configured already
//                   withMaven(maven: 'mvn3.6.1') {
//                       sh 'mvn -Dsonar.host.url=http://localhost:9005 sonar:sonar'
////                        sh 'mvn clean package sonar:sonar'
//                   }
//               }
//
//           }
//       }
//
//       stage("Quality Gate") {
//           steps {
//               timeout(time: 1, unit: 'HOURS') {
//                   // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
//                   // true = set pipeline to UNSTABLE, false = don't
//                   waitForQualityGate abortPipeline: false
//               }
//           }
//       }
       stage('Archival') {
           steps {
               publishHTML([allowMissing         : true,
                            alwaysLinkToLastBuild: false,
                            keepAll              : true,
                            reportDir            : 'target\\jacoco-report',
                            reportFiles          : 'index.html',
                            reportName           : 'Code Coverage',
                            reportTitles         : ''])


               publishHTML([allowMissing         : true,
                            alwaysLinkToLastBuild: false,
                            keepAll              : true,
                            reportDir            : 'target\\site',
                            reportFiles          : 'pmd.html',
                            reportName           : 'Static PMD report',
                            reportTitles         : ''])

               publishHTML([allowMissing         : true,
                            alwaysLinkToLastBuild: false,
                            keepAll              : true,
                            reportDir            : 'target\\site',
                            reportFiles          : 'cpd.html',
                            reportName           : 'CPD report',
                            reportTitles         : ''])


               step([$class     : 'JUnitResultArchiver',
                     testResults: 'target/surefire-reports/TEST-*.xml'])

               archiveArtifacts 'target/*.?ar'
               archiveArtifacts allowEmptyArchive: true, artifacts: '*.txt'
           }
       }
//   }
   post {
       always {
           junit testResults: '**/target/surefire-reports/TEST-*.xml'

           recordIssues enabledForFailure: true, tools: [mavenConsole(),
                                                         java()]
//            ,
//                                                          cpd(pattern: 'target\\cpd.xml'),
//                                                          pmdParser(pattern: 'target\\pmd.xml')]
////                                                         javaDoc(),checkStyle(), spotBugs()]

       }
   }
}