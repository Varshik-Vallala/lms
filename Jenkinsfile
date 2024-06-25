pipeline {
    agent any

    stages {
        stage('LMS-Code-Analysis') {
            steps {
                echo 'Sonar Analysis'
                sh 'cd webapp && sudo docker run  --rm -e SONAR_HOST_URL="http://20.81.42.125:9000" -e SONAR_LOGIN="sqp_6b7085d95f19623ff796984d91deec0699516ec0"  -v ".:/usr/src" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms-pipeline'
                echo 'Analysis Completed'
            }
        }

        stage('LMS-Build') {
            steps {
                echo 'Building LMS'
                sh 'cd webapp && npm install && npm run build'
                echo 'Building Completed'
            }
        }

        stage('LMS-Release') {
            steps {
                script {
                    echo "Publish LMS Artifacts"       
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    echo "${packageJSONVersion}"  
                    sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                    sh "curl -v -u admin:admin --upload-file webapp/lms-${packageJSONVersion}.zip http://20.81.42.125:8081/repository/lms/"     
            }
            }
        }

        stage('LMS-Deploy') {
            steps {
                script {
                     echo "Deploy LMS"       
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    echo "${packageJSONVersion}"  
                    sh "curl -u admin:admin -X GET \'http://20.81.42.125:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                    sh 'sudo rm -rf /var/www/html/*'
                    sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                    sh "sudo cp -r webapp/dist/* /var/www/html"
            }
            }
        }
    }
}

