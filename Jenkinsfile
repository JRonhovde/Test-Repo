node {
  currentBuild.displayName = "${BUILD_ID}"
}

pipeline {
  agent {
    label 'build-slave-02'
  }
  stages {     
    stage('StartAtScale(spark)') {
      steps {
        timestamps {
          checkout([$class: 'GitSCM', 
              branches: [[name: ':origin/config_templates']], 
              doGenerateSubmoduleConfigurations: false,
              extensions: [],
              submoduleCfg: [],
              userRemoteConfigs: [[credentialsId: '6ec3a612-5c5d-463b-bdc3-d501e1243793', url: 'git@github.com:AtScaleInc/docker.git']]
          ])
          sh 'cd dev-vm && ./bin/dev-vm-up.sh -b master --sql-engine atscale-spark --query-tester'
          sh 'docker exec -u atscaler atscale-01 /usr/local/atscale/bin/atscale_service_control restart atscale-spark'
          sh 'sleep 30'
        }  
      }
    }
    stage('Run Tests(spark)') {
      steps {
        sh 'docker pull docker.corp.atscale.com/atscale/query-tester:latest'
        sh 'docker run --net devvm_default docker.corp.atscale.com/atscale/query-tester:latest -h atscale-01 --authHost atscale-01 -g jenkins'
      }
    }
    stage('StartAtScale(hive)') {
      steps {
        timestamps {
          checkout([$class: 'GitSCM', 
              branches: [[name: ':origin/config_templates']], 
              doGenerateSubmoduleConfigurations: false,
              extensions: [],
              submoduleCfg: [],
              userRemoteConfigs: [[credentialsId: '6ec3a612-5c5d-463b-bdc3-d501e1243793', url: 'git@github.com:AtScaleInc/docker.git']]
          ])
          sh 'cd dev-vm && ./bin/dev-vm-up.sh -b master --sql-engine atscale-hive --query-tester'
        }
      }
    }
    stage('Run Tests(hive)') {
      steps {
        sh 'docker run --net devvm_default docker.corp.atscale.com/atscale/query-tester:latest -h atscale-01 --authHost atscale-01 -g jenkins'
      }
    }
    stage('Clean WS') {
      steps {
        cleanWs()
      }
    }
  }
}
