pipeline { 
  agent any 
 
  stages { 
    stage('Checkout') { 
      steps { 
        git branch: 'main', url: 'https://github.com/niyati-u/8.2CDevSecOps.git' 
      } 
    } 
 
    stage('Install Dependencies') { 
      steps { 
        sh 'npm install' 
      } 
    } 
 
    stage('Run Tests') { 
      steps { 
         // If tests fail (e.g., Snyk not authed), Jenkins would stop the pipeline and you'd never reach the Security Scan stage catchError marks THIS STAGE as FAILURE but lets the pipeline CONTINUE, so your Security Scan stage still runs and can send its email.
         
         catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
             sh 'npm test' 
        // If you want the pipeline to continue even if tests fail, use: sh 'npm test || true'
        }
      } 
      post {
        success {
          emailext(
            subject: "Test stage SUCCESS",
            body: "The Test stage completed successfully. Log attached.",
            attachLog: true,
            to: "nudani03@gmail.com"
          )
        }
        failure {
          emailext(
            subject: "Test stage FAILURE",
            body: "The Test stage failed. See attached log.",
            attachLog: true,
            to: "nudani03@gmail.com"
          )
        }
      }
    } 
 
    stage('Generate Coverage Report') { 
      steps { 
        // Ensure coverage report exists 
        sh 'npm run coverage || true' 
      } 
    } 
 
    stage('NPM Audit (Security Scan)') { 
      steps {
            
             //use catchError so the STAGE result is FAILURE (so email can state failure), but the pipeline doesn’t abort early.
         
         catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
             sh 'npm audit'
         // If you want to ignore audit failures and continue, use: sh 'npm audit || true'
        }
      }
      
       post {
        success {
          emailext(
            subject: "Security Scan SUCCESS",
            body: "The Security Scan completed successfully. Log attached.",
            attachLog: true,
            to: "nudani03@gmail.com"
          )
        }
        failure {
          emailext(
            subject: "Security Scan FAILURE",
            body: "The Security Scan failed. See attached log.",
            attachLog: true,
            to: "nudani03@gmail.com"
          )
        }
      }
    }
  }
}
