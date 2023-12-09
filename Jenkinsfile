pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            some-label: some-label-value
        spec:
          containers:
          - name: maven
            image: maven:alpine
            command:
            - cat
            tty: true
          - name: busybox
            image: busybox
            command:
            - cat
            tty: true
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
             - mountPath: /var/run/docker.sock
               name: docker-sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock   
        '''
      retries 2
    }
  }
  stages {
    stage('Run maven') {
      steps {
        container('maven') {
          sh 'mvn -version'
          sh 'ls -a'
        }
        container('busybox') {
          sh '/bin/busybox'
          sh 'ls -la'

        }
      }
    }
   stage('Docker-test') {
      steps {
        container('docker') {
          sh 'docker ps -a'
        }
      }
  }
  stage('List pods') {
    withKubeConfig([credentialsId: 'k8s-config']) {
        sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'  
        sh 'chmod u+x ./kubectl'  
        sh './kubectl get pods'
    }
  stage('kubectl') {
      steps {
        container('jnlp') {
          sh 'kubectl get pods'
        }
      }
    }
  }
}
