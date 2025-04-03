pipeline {
    agent any
    
    tools {
        maven 'Maven-3.6.2'
        jdk 'JAVA_8'
    }
    
    environment {
        DOCKER_IMAGE = 'thumbnailer'
        DOCKER_TAG = '1.0-SNAPSHOT'
    }
    
    triggers {
        githubPush()
    }
    
    stages {
        stage('Install') {
            steps {
                configFileProvider([configFile(fileId: 'Jenkins-Artifactory', variable: 'MAVEN_SETTINGS')]) {
                    sh "mvn -U -s $MAVEN_SETTINGS clean install"
                }
            }
        }
        
        // stage('Test Docker Image') {
        //     steps {
        //         script {
        //             sh 'docker rm -f thumbnailer || true'
                    
        //             sh """
        //                 docker run --name thumbnailer \
        //                     -v /home/ubuntu/examples:/pics \
        //                     ${DOCKER_IMAGE}:${DOCKER_TAG} \
        //                     ls /pics
        //             """
        //         }
        //     }
        // }

stage('Debug TIFF Support') {
    steps {
        // נקה קונטיינרים קודמים
        sh 'docker rm -f thumbnailer || true'
        
        // בדוק אם יש את קובץ ה-TIFF בתיקייה
        sh "ls -l /var/jenkins_home/workspace/t/examples/"
        
        // בדוק הרשאות ושנה אותן אם צריך
        sh "chmod -R 777 /var/jenkins_home/workspace/t/examples/"
        
        // הרץ את הקונטיינר בצורה רגילה
        sh "docker run --name thumbnailer -v /var/jenkins_home/workspace/t/examples:/pics thumbnailer:1.0-SNAPSHOT"
        
        // בדוק את הלוגים של הקונטיינר
        sh "docker logs thumbnailer"
        
        // בדוק את תוכן התיקייה אחרי הריצה
        sh "ls -l /var/jenkins_home/workspace/t/examples/"
    }
}    }
    
    post {
        always {
            sh 'docker rm -f thumbnailer || true'
        }
    }
}
