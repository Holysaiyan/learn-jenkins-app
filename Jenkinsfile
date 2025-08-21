pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        cleanWs()         // wipes the workspace first
        checkout scm
      }
    }

    stage('Build') {
      agent {
        docker {
          image 'node:18-alpine'       // or: node:18-bullseye for glibc
          // match jenkins uid:gid if needed so files are writable
          args '-u 106:108'
        }
      }
      steps {
        sh '''
          node -v
          npm -v

          # optional: if you have native deps, install build tools on Alpine
          # apk add --no-cache python3 make g++

          # clean, deterministic install
          npm ci --no-audit --no-fund

          # build & test
          npm run build
          # npm test -- --ci
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'build/**', fingerprint: true
        }
      }
    }
  }
}
