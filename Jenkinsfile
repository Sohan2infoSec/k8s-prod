import java.text.SimpleDateFormat

pipeline {
  options {
    buildDiscarder logRotator(numToKeepStr: '5')
    disableConcurrentBuilds()
  }
  agent {
    kubernetes {
      cloud "kubernetes"
      label "prod"
      serviceAccount "build"
      yamlFile "KubernetesPod.yaml"
    }      
  }
  environment {
    cmAddr = "cm.61.28.237.12.nip.io"
  }
  stages {
    stage("deploy") {
      when {
        branch "master"
      }
      steps {
        container("helm") {
          sh "helm repo add chartmuseum http://${cmAddr}"
          sh "helm repo update"
          sh "helm dependency update helm"
          sh "helm upgrade -i helm helm/ --namespace prod --force"
        }
      }
    }
    stage("test") {
      when {
        branch "master"
      }
      steps {
        echo "Testing..."
      }
      post {
        failure {
          container("helm") {
            sh "helm rollback helm 0"
          }
        }
      }
    }
  }
}
