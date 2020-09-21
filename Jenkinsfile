pipeline {
  agent any
  stages {
    stage('Build') {
      parallel {
        stage('Start') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            echo 'Start'
          }
        }

        stage('Build  code') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'mvn -U clean package -Dir=/war; project_name=$(xml_grep --text_only \'/project/name\' pom.xml); project_version=$(xml_grep --text_only \'/project/version\' pom.xml);mkdir /war/${JOB_NAME}/arsiv   -p ; cp null/$project_name-$project_version.war /war/${JOB_NAME}/ -r ;times=$(date "+%m.%d.%y-%H:%M:%S");mkdir /war/${JOB_NAME}/arsiv/time    -p ; cp null/$project_name-$project_version.war /war/${JOB_NAME}/arsiv/time/$project_name-$project_version-$times.war -r;mkdir /war/${JOB_NAME}/arsiv/version -p ; cp null/$project_name-$project_version.war /war/${JOB_NAME}/arsiv/version/$project_name-$project_version.war -r;mkdir -p /war/${JOB_NAME}/deployinfo/; echo "/war/${JOB_NAME}/$project_name-$project_version.war" >/war/${JOB_NAME}/deployinfo/deploywar.info  '
            sh 'printenv;previous_version=$(ls -ltrh /war/${JOB_NAME}/arsiv/version/ |tail -2|head -1| awk  \'{print $9,$10,$11}\');echo   "/war/${JOB_NAME}/arsiv/version/${previous_version}"'
          }
        }

      }
    }

    stage('Test') {
      parallel {
        stage('Deploy war  all Tomcats ') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'warfile=$(cat /war/${JOB_NAME}/deployinfo/deploywar.info); python3 /code/tomcat_publisher.py -t "${test_tomcats_nodes}" -w  "${warfile}"'
          }
        }

        stage('Tester Confirmation') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            input(message: 'Deploy or not', id: 'Deploy or not', ok: 'go go go')
          }
        }

      }
    }

    stage('Deploy') {
      parallel {
        stage('Deploy start ') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            echo 'deploy start '
          }
        }

        stage('deploy tomcats ') {
          steps {
            echo 'tomcats deploy '
            sh 'warfile=$(cat /war/${JOB_NAME}/deployinfo/deploywar.info); python3 /code/tomcat_publisher.py -t "${tomcats_nodes}" -w  "${warfile}"'
          }
        }

      }
    }
 stage('Stage 2') {
      agent none
      steps {
        timeout(time: 1, unit: 'MINUTES') {
          script {
            echo 'This stage does not block an executor because of "agent none"'
            milestone 1
            inputResponse = input([
              message           : 'Please confirm.',
              submitterParameter: 'submitter',
              parameters        : [
                [$class: 'BooleanParameterDefinition', defaultValue: true, name: 'param1', description: 'description1'],
                [$class: 'ChoiceParameterDefinition', choices: 'choice1\nchoice2', name: 'param2', description: 'description2']
              ]
            ])
            milestone 2
            echo "Input response: ${inputResponse}"
          }
        }
      }
    }

    stage('UnDeploy') {
      steps {
        input(message: 'undeploy', id: 'undeploy', ok: 'undeploy', submitter: 'undeploy', submitterParameter: 'undeploy')
        sh 'previous_version=$(ls -ltrh /war/${JOB_NAME}/arsiv/version/$path|tail -2|head -1| awk  \'{print $9,$10,$11}\');python3 /code/tomcat_publisher.py -t "${tomcats_nodes}" -w  "/war/${JOB_NAME}/arsiv/version/${previous_version}"'
      }
    }

   
  }
  environment {
    testnodeip = '192.168.1.126'
    deploynodeip = '192.168.1.127'
    master = '192.168.1.125'
    developer = 'developer'
    tester = 'developer'
    cicdcontrol = '192.168.1.39:5000'
    tomcats_nodes = '{"1":{"name":"test","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample","address_alias":"www.sebit.com"},"2":{"name":"test2","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample3","address_alias":"www.sebit.com"}}'
    test_tomcats_nodes = '{"1":{"name":"test","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample","address_alias":"www.sebit.com"},"2":{"name":"test2","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample3","address_alias":"www.sebit.com"}}'
  }
}
