pipeline {
  agent {
    label 'linux'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: scm.branches,
          doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
          extensions: scm.extensions + [[$class: 'CleanBeforeCheckout'], [$class: 'LocalBranch', localBranch: "**"]],
          userRemoteConfigs: scm.userRemoteConfigs,
        ])
      }
    }
    stage('publishing helm chart') {
      agent {
        docker {
          label 'linux'
          image 'dtzar/helm-kubectl:2.16.1'
          args '''-v /var/run/docker.sock:/var/run/docker.sock \
                    -v /data/config:/data/config \
                    -h $HOSTNAME \
                    --cap-add SYS_ADMIN \
                    --cap-add DAC_READ_SEARCH
                '''
        }
      }
      steps{
        sh "helm init"
        sh "helm plugin install https://github.com/chartmuseum/helm-push"
        sh "helm repo add helm http://helm.dihalk.com"
        sh "helm push . helm"
      }
    }
  }
}
