pipeline {
  agent any
  environment {
    APP_NAME = "${JOB_NAME}"
    //HARBOR_HOST = 'registry.cn-hangzhou.aliyuncs.com' //镜像仓库地址
    //DOCKER_IMAGE = 'yshome' //镜像仓库名
    //Tag = "J${BUILD_NUMBER}" //镜像版本
    //Home = '/home' //服务器家目录
    //APP_URL = 'http://10.10.22.186:8088' //应用访问路径
    DING_TALK = '03eda619-0627-4a8a-8c0b-4955c3bb5bf5'

  }
  stages {
    stage('钉钉开始通知') {
      steps{
        sh '''
            printenv
        '''
        dingtalk(
                robot: '${DING_TALK}',   
                type:'ACTION_CARD',
                title: "您有新消息，请注意查收",
                text: ['![](http://ad.cdyushun.com/jenkins.png)',
                '* 项目: ${APP_NAME}',
                '* 分支：${GIT_BRANCH}',
                '* 状态: <font color=#40E0D0>开始</font>',
                '* 开始时间: ${BUILD_TIMESTAMP}',
                '* 执行人: ${gitlabUserName}'
                ]
        )
      }  
    }
 /**    stage('编译打包') {
      agent { 
        docker {
          image 'node:latest'
        }
      }
      steps {
          sh '''
                yarn config set registry https://registry.npm.taobao.org
                yarn
                yarn build
          '''      
          }
    }

    stage('构建镜像'){
      agent any
      steps {
        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'aliyun', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]){
          sh '''
                docker login ${HARBOR_HOST} -u ${USERNAME} -p ${PASSWORD}
                docker build -t ${HARBOR_HOST}/${DOCKER_IMAGE}/${APP_NAME}:${Tag} .
                docker push ${HARBOR_HOST}/${DOCKER_IMAGE}/${APP_NAME}:${Tag}          
                docker rmi  ${HARBOR_HOST}/${DOCKER_IMAGE}/${APP_NAME}:${Tag}
          '''
          }
      }
    }

    stage('部署'){
      agent {
        docker {
          image 'ansible:latest'
          args "-v /root/.ssh:/root/.ssh -v ${Home}/${APP_NAME}/etc/hosts:/etc/ansible/hosts -v ${Home}/${APP_NAME}/conf:/tmp "
        }
      }
      steps {
        sh '''
          ansible-playbook -e Tag=${Tag} -e APP_NAME=${APP_NAME} ansible/main.yml
        '''
      }
    }
  }
*//
  post {
    success {

      
      dingtalk (

                robot: '${DING_TALK}',   
                type:'ACTION_CARD',
                title: "您有新消息，请注意查收",
                text: ['![](http://ad.cdyushun.com/jenkins.png)',
                '* 项目: ${APP_NAME}',
                '* 分支：${GIT_BRANCH}',
                '* 状态: <font color=#32CD32>成功</font>',
                '* 执行人: ${gitlabUserName}',
                '* [立即访问](${APP_URL})']



      )
    }

    failure {
      dingtalk (
                robot: '${DING_TALK}',   
                type:'ACTION_CARD',
                title: "您有新消息，请注意查收",
                text: ['![](http://ad.cdyushun.com/jenkins.png)',
                '* 项目: ${APP_NAME}',
                '* 分支：${GIT_BRANCH}',
                '* 状态: <font color=#FF0000>失败</font>',
                '* 执行人: ${gitlabUserName}']
      )
    }
  }
   
}
