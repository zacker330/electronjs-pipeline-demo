pipeline {
  agent none

  environment {
    __version = ""
    __appName = "electron-webpack-quick-start"
  }

  stages {
    stage("生成版本号"){
      agent {label "linux" }
      steps{
        script{
            __version = generateVersion("1.0.0")
            echo "version is ${__version}"
        }
      }
    }
    stage('并行构建') {
      failFast true
      parallel {
        stage('Windows平台下构建') {
          agent {label "windows && nodejs" }
          steps {
            echo "${__version}"
          }
        }
        stage('Linux平台下构建') {
          agent {label  "linux && nodejs" }
          environment{
              __suffix = "tar.xz"
              __artifactPath = "dist/${__appName}-${__version}.${__suffix}"
          }
          steps {
            script{
                nodejs(nodeJSInstallationName: 'nodejs10.15.2') {
                  sh "npm install yarn"
                  sh "yarn version --new-version ${__version}"
                  sh "yarn install"
                  sh "yarn dist --linux deb ${__suffix}"
                  uploadArtifact("${__appName}", "${__version}", "${__artifactPath}")
                }
            }
              
          }
        }
        stage('Mac平台下构建') {
          agent {label "mac && nodejs" }
          stages {
            stage('mac 下阶段1') {
              steps {
                  echo "staging 1"
              }
            }
            stage('mac 下阶段2') {
              steps {
                  echo "staging 2"
              }
            }
          }
        }
      }
    }
    stage("其它阶段，读者可根据情况自行添加"){
      agent {label "linux"}
      steps{
          echo "发布"
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}

def generateVersion(def ver){
  def gitCommitId = env.GIT_COMMIT.take(7)
  return "${ver}-${gitCommitId}.${env.BUILD_NUMBER}"
}

def uploadArtifact(def appName, def appVersion, def artifactPath){
  echo "根据参数将制品上传到制品库中，待测试"
}