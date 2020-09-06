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
            sh 'echo "                Database engine is ${testnodeip}"'
          }
        }

        stage('builder node ') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            sh 'mvn -U clean package '
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
            sh 'pwd'
            sh 'whoami'
            sh 'tomcat-maneger connect http://172.16.232.230:8080/manager admin password  deploy local *.war /hellowor -v v2.0.6 '
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
    tomcat_nodes = ' [ {"name": "tomcat","ip":"172.16.232.230"},{"name": "tomcat2","ip":"172.16.232"}]'
    tomcat_port = '8080'
    tomcat_username = 'admin'
    tomcat_password = 'admin'
    tomcat_host = '172.16.232.230'
    context_path = 'myApplication'
    artifact = 'artifact'
  }
}