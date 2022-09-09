podTemplate(containers: [
    containerTemplate(
        name: 'gradle',
        image: 'gradle:7.3.0-jdk17',
        command: 'sleep',args: '99d',
        resourceLimitCpu: '500m',
        resourceLimitMemory: '2048Mi'
    ),
    containerTemplate(
        name: 'docker',
        image: 'docker',
        command: 'cat',
        ttyEnabled: true,
        resourceLimitCpu: '100m',
        resourceLimitMemory: '200Mi'
    )
  ],
  volumes: [
       hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ],
  envVars: [
  secretEnvVar(key: 'uname', secretName: 'credrl', secretKey: 'username'),
  secretEnvVar(key: 'pwd', secretName: 'credrl', secretKey: 'password'),
  secretEnvVar(key: 'artifactoryUser', secretName: 'credrl', secretKey: 'username')
  ],
  imagePullSecrets:
  ['regcred']
   )
  {
    node(POD_LABEL) {
        stage('Get a Java project from git') {
            sh"git config --global http.sslverify false"
            sh"export GIT_SSL_NO_VERIFY=true"
            git branch: 'main', url: 'https://github.com/branko129/jenkins-webhook-test.git'
            script{
            latestTag = sh(returnStdout:  true, script: "git tag --sort=-creatordate | head -n 1").trim()
            latestHash = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
            env.GIT_COMMIT_MSG = sh (script: 'git log -1 --pretty=%B ${GIT_COMMIT}', returnStdout: true).trim()
            env.GIT_AUTHOR = sh (script: 'git log -1 --pretty=%cn ${GIT_COMMIT}', returnStdout: true).trim()
            env.BUILD_VERSION = latestTag
            env.HASH_TAG = latestHash
            echo "env-BUILD_VERSION = ${env.BUILD_VERSION}"
            echo "env-HASH_TAG = ${env.HASH_TAG}"
            echo "author = ${env.GIT_AUTHOR}"
            echo "commit message = ${env.GIT_COMMIT_MSG}"
            }
            if ((sh (script: 'git log -1 --pretty=%cn ${GIT_COMMIT}', returnStdout: true).trim()) == "branko"){
              currentBuild.result = 'SUCCESS'
              return
            }
        }
        stage('Print something'){
           script{
              echo "Commiter nije branko"
           }
        }
    }
  }
