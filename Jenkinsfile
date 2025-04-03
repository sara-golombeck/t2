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

        stage('Test Docker Image') {
    steps {
        script {
            sh 'docker rm -f thumbnailer || true'
            
            // הרצת הקונטיינר ללא פקודות נוספות - יפעיל את entrypoint.sh שאמור ליצור thumbnails
            sh """
                docker run --name thumbnailer \
                    -v /home/ubuntu/examples:/pics \
                    ${DOCKER_IMAGE}:${DOCKER_TAG}
            """
            
            // בדיקה שנוצרו תמונות ממוזערות
            sh """
                # שכפול קבצי הדוגמאות לתיקייה זמנית לבדיקה
                mkdir -p /tmp/thumbnailer-check
                cp -r /home/ubuntu/examples/* /tmp/thumbnailer-check/
                ls -la /tmp/thumbnailer-check/
                
                # בדיקה ספציפית אם נוצרו קבצי thumbnail עבור TIFF
                if [ -f /tmp/thumbnailer-check/tn-jenkins2.png ]; then
                    echo "TIFF thumbnail created successfully!"
                else
                    echo "FAILED: TIFF thumbnail not created"
                    exit 1
                fi
            """
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
