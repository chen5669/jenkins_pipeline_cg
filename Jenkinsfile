pipeline {
    agent any
    //jenkins pipeline triggers cron 每两份中触发一次
    tools {
        jdk 'jdk8'
        maven 'M2'
    }
    triggers{
//         cron('H/2 * * * *')
//         每分钟判断一次代码仓库是否有变化
        pollSCM('H/1 * * * *')
//         定期检查开发代码更新，工作日每晚4点做daily build
//         pollSCM('H 4 * * 1-5')
    }



    parameters {
        //git代码路径【参数值对外隐藏】
        string(name:'repoUrl', defaultValue: 'git@github.com:Mendol/game-of-life.git', description: 'git代码路径')
        //repoBranch参数后续替换成git parameter不再依赖手工输入,JENKINS-46451【git parameters目前还不支持pipeline】
        string(name:'repoBranch', defaultValue: 'master', description: 'git分支名称')
        //pom.xml的相对路径
        string(name:'pomPath', defaultValue: 'pom.xml', description: 'pom.xml的相对路径')
        //war包的相对路径
        string(name:'warLocation', defaultValue: 'gameoflife-web/target/*.war', description: 'war包的相对路径 ')

        //服务器参数采用了组合方式，避免多次选择，使用docker为更佳实践【参数值对外隐藏】
        choice(name: 'env',choices:['192.168.0.115,8090','test','stage','prod'], description: '选择部署环境')

        //测试服务器的dubbo服务端口
        string(name:'dubboPort', defaultValue: '31100', description: '测试服务器的dubbo服务端口')
        //单元测试代码覆盖率要求，各项目视要求调整参数
        string(name:'lineCoverage', defaultValue: '20', description: '单元测试代码覆盖率要求(%)，小于此值pipeline将会失败！')
        //若勾选在pipelie完成后会邮件通知测试人员进行验收
        booleanParam(name: 'isCommitQA',description: '是否邮件通知测试人员进行人工验收',defaultValue: false )
        }


    //保持构建的最大个数
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    //1.自定义pipeline 环境变量
    //2. 环境变量间相互引用
    environment {
        __Author = "chenguang09"
        __Server_Name = "Jenkins pipeline!!!!!!"
        __Merge = "service name ${__Server_Name} By ${__Author}"
        __Java_Version = ""
    }

    stages {
        stage('Example') {
                steps {
                    sh 'java -version'
                    echo "java -version"
                }
        }
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
              configFileProvider([configFile(fileId: 'maven-global-settings', variable: 'MAVEN_SETTINGS')]) {
                                  sh 'mvn -s $MAVEN_SETTINGS clean package'
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
