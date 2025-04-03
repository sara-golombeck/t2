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
        script {
            sh 'docker rm -f thumbnailer || true'
            
            // הראה תוכן התיקייה לפני
            sh "ls -la /var/jenkins_home/workspace/t/examples/"
            
            // הרץ קונטיינר אינטראקטיבי שנשאר פתוח
            sh """
                docker run -d --name thumbnailer \
                    -v /var/jenkins_home/workspace/t/examples:/pics \
                    ${DOCKER_IMAGE}:${DOCKER_TAG} \
                    tail -f /dev/null
            """
            
            // היכנס לקונטיינר ובדוק אם אתה יכול ליצור את התמונות הממוזערות באופן ידני
            sh """
                docker exec thumbnailer ls -la /pics/
                docker exec thumbnailer bash -c 'cd / && ./entrypoint.sh'
                docker exec thumbnailer ls -la /pics/
            """
            
            // בדוק אם הקבצים נוצרו
            sh "ls -la /var/jenkins_home/workspace/t/examples/"
        }
    }
}
    }
    
    post {
        always {
            sh 'docker rm -f thumbnailer || true'
        }
    }
}
