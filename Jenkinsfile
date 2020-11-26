pipeline {
    agent { label 'docker-agent' }
    triggers { pollSCM ('* * * * *') }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'wrapper', credentialsId: 'github-adedj', url: 'https://github.com/adedj/HelloWorld.git'
        }
    }
        stage('Build') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'webproxy', passwordVariable: 'PROXY_PASS', usernameVariable: 'PROXY_USER'), usernamePassword(credentialsId: 'artifactory', passwordVariable: 'MVNW_PASSWORD', usernameVariable: 'MVNW_USERNAME')]) {
                    sh '''export JAVA_HOME=/usr/local/openjdk-8
                         export MVNW_REPOURL=https://repo.mcvl-engineering.com/repository/ips-group
                         export MAVEN_OPTS="-Dhttp.proxyHost=webaccess2.office.local -Dhttp.proxyPort=8080 -Dhttps.proxyHost=webaccess2.office.local -Dhttps.proxyPort=8080 -Dhttp.proxyUser=$PROXY_USER -Dhttps.proxyUser=$PROXY_USER -Dhttp.proxyPassword=$PROXY_PASS -Dhttps.proxyPassword=$PROXY_PASS -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true"
                         ./mvnw package'''
                }
            }

            post {
                success {
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}
