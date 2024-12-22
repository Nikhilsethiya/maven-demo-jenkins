node {
    def mavenTool = tool name: '3.9.9', type: 'maven'
    def jdkTool = tool name: 'jdk-21', type: 'jdk'

    def skipStages = false

    try {
        stage('Build') {
            withEnv(["JAVA_HOME=${jdkTool}", "PATH+MAVEN=${mavenTool}/bin"]) { 
                sh 'mvn clean package' 
            }
        }

        stage('Test') {
            withEnv(["JAVA_HOME=${jdkTool}", "PATH+MAVEN=${mavenTool}/bin"]) {
                try {
                    sh 'mvn test'
                } finally {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Deploy') {
            withEnv(["JAVA_HOME=${jdkTool}", "PATH+MAVEN=${mavenTool}/bin"]) {
                sh './jenkins/scripts/deliver.sh' 
                input message: 'Sudah selesai menggunakan Java App? (Klik "Proceed" untuk mengakhiri)'
            }
        }
    } catch (e) {
        echo "Build failed in stage '${currentBuild.currentResult}': ${e.getMessage()}"
        currentBuild.result = 'UNSTABLE' 
    } finally {
        if (currentBuild.result == 'UNSTABLE') {
            echo "Pipeline marked as unstable." 
        }
    }
}

