// Uses Declarative syntax to run commands inside a container.
pipeline {
  agent {
      kubernetes {
        nodeSelector 'beta.kubernetes.io/os=linux' 
        defaultContainer 'maven'
        idleMinutes 5
        yaml '''
apiVersion: v1
kind: Pod
spec:
  volumes:
    - name: workspace-volume
      persistentVolumeClaim:
        claimName: cache-pvc
  containers:
  - name: jnlp
    image: jenkins/inbound-agent
    env:
      - name: HOME
        value: /home/jenkins
    resources:
      limits:
        memory: "1Gi"
      requests:
        memory: "256Mi"
  - name: maven
    image: jenkins/jnlp-agent-maven:jdk11
    command:
    - sleep
    args:
    - infinity
    env:
      - name: HOME
        value: /home/jenkins
    volumeMounts:
      - name: workspace-volume
        mountPath: "/home/jenkins"
'''
        }
    }
    stages {
        stage('Main') {
            steps {
                container('maven') {
                  sh 'mvn -version'
                  sh 'ls -last /home/jenkins $HOME'
                  sh 'env | sort'
                }
            }
        }
        stage('maven') {
            steps {
                container('maven') {
                    sh 'mvn clean install'
                }
            }
        }
    }
}
