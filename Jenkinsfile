def projectName = 'EDA-tool'
def version = "0.0.${currentBuild.number}"
def dockerImageTag = "${projectName}:${version}"

pipeline {
  agent any

  stages {

    stage('Build') {
      steps {
        sh """
        chmod +x -R ${env.WORKSPACE}
        ./dev/Scripts/activate
        pwd
        python3.8 --version
        python3.8 -m pip install --user -r /var/lib/jenkins/workspace/EDA_pipeline/requirements.txt
        python3.8 /var/lib/jenkins/workspace/EDA_pipeline/app.py"""
        
      }
    }

    stage('Build Container') {
      steps {
        sh "docker build -t ${dockerImageTag} ."
      }
    }

    stage('Deploy Container To Openshift') {
      steps {
        sh "oc login https://localhost:8443 --username admin --password admin --insecure-skip-tls-verify=true"
        sh "oc project ${projectName} || oc new-project ${projectName}"
        sh "oc delete all --selector app=${projectName} || echo 'Unable to delete all previous openshift resources'"
        sh "oc new-app ${dockerImageTag} -l version=${version}"
        sh "oc expose svc/${projectName}"
      }
    }
  }
}