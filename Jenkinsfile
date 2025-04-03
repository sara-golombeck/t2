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

stage('Test TIFF Support') {
    steps {
        script {
            sh 'docker rm -f thumbnailer || true'
            
            // הרץ את הקונטיינר עם volume מותאם לדוגמאות
            sh """
                docker run --name thumbnailer \
                    -v ${WORKSPACE}/examples:/pics \
                    ${DOCKER_IMAGE}:${DOCKER_TAG}
            """
            
            // הצג את הלוגים של הקונטיינר
            sh "docker logs thumbnailer"
            
            // בדוק ישירות אם הקובץ נוצר בתיקיית examples
            sh """
                if [ -f ${WORKSPACE}/examples/tn-jenkins2.png ]; then
                    echo "===== SUCCESS: TIFF Support Working! ====="
                    ls -la ${WORKSPACE}/examples/tn-jenkins2.png
                else
                    echo "===== FAILED: TIFF thumbnail not created ====="
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
