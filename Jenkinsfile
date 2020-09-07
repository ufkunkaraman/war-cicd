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
            sh 'mvn -U clean package -Dir=/war; project_name=$(xml_grep --text_only \'/project/name\' pom.xml); project_version=$(xml_grep --text_only \'/project/version\' pom.xml);mkdir /war/${JOB_NAME}/   -p ; cp null/$project_name-$project_version.war /war/${JOB_NAME}/ -r ;mkdir /war/${JOB_NAME}/arsiv -p ;times=$(date "+%m.%d.%y-%H:%M:%S");cp null/$project_name-$project_version.war /war/${JOB_NAME}/arsiv/$project_name-$project_version-$times.war -r; echo "/war/${JOB_NAME}/$project_name-$project_version.war" >/war/${JOB_NAME}/arsiv/deploywar.info  '
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
            sh 'warfile=$(cat /war/${JOB_NAME}/arsiv/deploywar.info); python3 /code/tomcat_publisher.py -t "${test_tomcats_nodes}" -w  "${warfile}" -o "test"'
          }
        }

        stage('Tester Confirmation') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'for i in {0..$(echo \'${tomcat_nodes}\' | jq \'length\')}; do  echo """Number: $i , ip=$(echo "${tomcat_nodes}"|jq ".[$i].ip") """; done'
            sh '''curl -v -u ${tomcat_username}:${tomcat_password} -T ${artifact} \'http://\'${tomcat_host}\':\'${tomcat_port}\'/manager/text/deploy?path=//\'${context_path}\'\'
'''
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
            sh '                echo "Database engine is ${testnodeip}"'
          }
        }

        stage('deploy code') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'python3 app.py'
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
    tomcat_nodes = '{"1":{"name":"test","address":"172.16.232.230:8080","username":"admin","password":"password","tomcat_path":"/sample"},"2":{"name":"test","address":"172.16.232.230:8080","username":"admin","password":"password","tomcat_path":"/sample3"}}'
    tomcat_port = '8080'
    tomcat_username = 'admin'
    tomcat_password = 'admin'
    tomcat_host = '172.16.232.230'
    context_path = 'myApplication'
    artifact = 'artifact'
    test_tomcats_nodes = '{"1":{"name":"test","address":"172.16.232.230:8080","username":"admin","password":"password","tomcat_path":"/sample"},"2":{"name":"test","address":"172.16.232.230:8080","username":"admin","password":"password","tomcat_path":"/sample3"}}'
  }
}