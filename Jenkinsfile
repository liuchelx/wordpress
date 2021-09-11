pipeline {
    /*** Jenkins 选项和运行时选 */
    
    agent {
        node { label "python-pipeline" }  //选择运行的agent
        }

    options{
      timestamps()
      disableConcurrentBuilds()             // 禁止并行，每次只允许一个构建。
      timeout(time: 30, unit: 'MINUTES')    // java.util.concurrent.TimeUnit
    }

    /*** 自定义环境变量*/

    environment {
        GIT_URL       = 'https://github.com/liuchelx/wordpress.git'
        CREDENTIAL    = 'git'               //从jenkins credential拿来的，用于git登录
        BRANCH        = 'main'              //工作的branch
        NEXUS_CREDENTIAL = "nexus"              //同样从jenkins credential拿来的，用nexus登录
    }

    /*** 编译过程由多个 Stage(阶段)构成.*/

    stages {
        /*** 每个阶段下可再分 step*/

        stage('Clear up workspace') {
            steps {
                sh 'printenv'                               // 打印环境变量

                echo "Delete workspace ${workspace}"        // 清理缓存缓存，以及之前的残留文件

                dir("${workspace}") {
                    deleteDir()
                }
                dir("${workspace}@tmp") {
                    deleteDir()
                }
            }
        }

        /*** 从git上拉取文件*/

        stage('Get code from Github') {
            steps {
                script{                                     // Java script
                  println('Get code from Github')           // println 等效于 echo
                }
                git branch: "${env.BRANCH}", credentialsId: "${env.CREDENTIAL}", url: "${env.GIT_URL}"   // 使用 env 名称空间下的环境变量
            }
        }


        /*** Package*
        * 关于 Python 版本规范：https://www.python.org/dev/peps/pep-0440/
        */

        stage('Build') {
            steps {
                echo 'Build project'     
                sh 'cd /var/jenkins/workspace/wordpress/applications/wordpress/DockerFile'
                sh 'docker build -t 150.230.33.152:8083/wordpress_pipe .'   
            }
        }

        stage('Pushing to Repository') {
            steps {
                echo 'Upload project'
                sh 'echo "${nexus_password}" | docker login -u admin --password-stdin  150.230.33.152:8083'
                sh 'docker push 150.230.33.152:8083/wordpress_pipe'
            }
        }
    }

    /*** 构建后行为*/

    post{
        // 总是执行
        always{
            echo "Always"
        }

        // 条件执行
        success {
            echo currentBuild.description = "Success"    // currentBuild.description 会将信息带回控制面板
            /**
            emailext (
                subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                    <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']]
            )
            */
        }

        failure{
            echo  currentBuild.description = "Failure"
            /**
            emailext (
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                    <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']]                                            
            )
            */
        }

        aborted{
            echo currentBuild.description = "Aborted"
            /**
            emailext (
                subject: "Aborted: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>ABORTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                    <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']]                                            
            )
            */
        }
    }
}
