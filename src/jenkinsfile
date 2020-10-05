pipeline {
    agent any
    //jenkins pipeline triggers cron 每两份中触发一次
    triggers{
        //cron('H/2 * * * *')
        //每分钟判断一次代码仓库是否有变化
        pollSCM('H/1 * * * *')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    //1.自定义pipeline 环境变量
    //2. 环境变量间相互引用
    environment {
        __Author = "chenguang09"
        __Server_Name = "Jenkins pipeline!!!!!!"
        __Merge = "service name ${__Server_Name} By ${__Author}"
    }

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
                sh'printenv'
            }
        }

        stage('更新拉取最新代码') {
              steps {
                git branch: "master", url: 'https://github.com/chen5669/jenkins_maven_pipeline.git/'
              }
            }

        //stage('pmd') {
        //    steps {
        //        sh "mvn pmd:pmd"
        //     }
        //}
        stage('Test') {
            steps {
                echo "Running on ${env.BUILD_NUMBER} on ${env.JENKINS_URL}"
                echo "git branch is ${env.GIT_BRANCH}"
                echo "Job '${JOB_NAME}' (${BUILD_NUMBER}) is waiting for input"
                echo "Please go to ${BUILD_URL} and verify the build"
                echo "The author is ${__Author}"
                echo "${__Merge}"
                echo "自定义全局环境变量g_name: ${env.g_name}"

            }
        }
    }
    post {
            always {
                echo 'I will always say Hello!'
                echo "The author is ${__Author}"
                pmd(canRunOnFailed: true, pattern: '**/target/pmd.xml')
            }
            aborted {
                echo 'I was aborted'
            }
            failure {
                //mail to: 'cgsnd5669@163.com',
                //subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                //body: "Something is wrong with ${env.BUILD_URL}"
                echo 'post failure !!!'
            }
            success {
                echo 'post success !!!'
            }
        }
}
