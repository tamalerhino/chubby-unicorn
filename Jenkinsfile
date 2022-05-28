pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t "${IMAGENAME}" .'
      }
    }
    stage('Scan') {
      steps {
        sh 'trivy image --exit-code 1 --severity MEDIUM,HIGH,CRITICAL "${IMAGENAME}"'
      }
    }
    
    stage('Create SBOM') {
      steps {
        sh 'trivy --output "${IMAGENAME}".json sbom "${IMAGENAME}"'
      }
    }
    stage('Upload Artifacts') {
      steps {
        sh 'docker login "${CONTAINER-REPO-URL}" -u="${CONTAINER-REPO-USERNAME}" -p="${CONTAINER-REPO-PASSWORD]"'
        sh 'docker tag "${IMAGENAME}" "${CONTAINERPROJECT}"/"${IMAGENAME}"'
        sh 'docker push "${CONTAINER-REPO-URL}"/"${CONTAINERPROJECT}"/"${IMAGENAME}"'
      }
       steps {
        sh 'curl -u "${USERNAME}":"${PASSWORD}" -X PUT  "${ARTIFACTORY-RUL}/"${CONTAINERPROJECT}"/"${IMAGENAME}".json" -T "${IMAGENAME}".json'
      }
    }
  }
}
