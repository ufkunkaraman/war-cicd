pipeline {
  agent any
  stages {
    stage('Build') {
      parallel {
        stage('Developer Confirmation') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'echo "Git branch is ${GIT_BRANCH}";echo "Git url is ${GIT_URL}";echo "Job name is ${JOB_NAME}"'
            sh ' export project_name=$(xml_grep --text_only \'/project/name\' pom.xml); export project_version=$(xml_grep --text_only \'/project/version\' pom.xml)'
          }
        }

        stage('builder node ') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'mvn -U clean package -Dir=/war; project_name=$(xml_grep --text_only \'/project/name\' pom.xml); project_version=$(xml_grep --text_only \'/project/version\' pom.xml);mkdir /war/${JOB_NAME}/   -p ; cp null/$project_name-$project_version.war /war/${JOB_NAME}/ -r ;mkdir /war/${JOB_NAME}/arsiv -p ;times=$(date "+%m.%d.%y-%H:%M:%S");cp null/$project_name-$project_version.war /war/${JOB_NAME}/arsiv/$project_name-$project_version-$times.war -r;mkdir -p /war/${JOB_NAME}/deployinfo/; echo "/war/${JOB_NAME}/$project_name-$project_version.war" >/war/${JOB_NAME}/deployinfo/deploywar.info  '
          }
        }

        stage('print env') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'printenv'
            sh 'pwd;ls -ltrh '
            sh 'echo "${project_name}"'
          }
        }

      }
    }

    stage('Test') {
      parallel {
        stage('Run Code') {
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
            sh 'echo "test"'
            sh 'echo "test"'
            input(message: 'Deploy or not', id: 'Deploy or not', ok: 'go go go')
          }
        }

      }
    }

    stage('Deploy') {
      parallel {
        stage('Developer Corfirmation') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'warfile=$(cat /war/${JOB_NAME}/deployinfo//deploywar.info); python3 /code/tomcat_publisher.py -t "${tomcat_nodes}" -w  "${warfile}" -o "test"'
          }
        }

        stage('deploy code') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            echo 's'
          }
        }

      }
    }

    stage('Deploy Control') {
      parallel {
        stage('Deploy Control') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            echo 'deploy control developer'
            sh 'echo "control code if success or not"'
          }
        }

        stage('finish') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            echo 'finish'
          }
        }

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
    tomcat_nodes = '{"1":{"name":"test","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample","address_alias":"www.sebit.com"},"2":{"name":"test2","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample3","address_alias":"www.sebit.com"}}'
    test_tomcats_nodes = '{"1":{"name":"test","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample","address_alias":"www.sebit.com"},"2":{"name":"test2","address":"172.16.232.230","port":"8080","username":"admin","password":"password","tomcat_path":"/sample3","address_alias":"www.sebit.com"}}'
  }
}