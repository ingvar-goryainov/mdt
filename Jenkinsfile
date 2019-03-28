node('Slave_node_1') {

    currentBuild.result = "SUCCESS"

    try {
    
        stage ('Checkout') {
            checkout scm
        }
        
        stage ('Build') {
            nodejs('Node10') {
                // some block
                sh '''
                cd "$WORKSPACE/www/css"
                cleancss -d style.css > ../min/custom-min.css

                cd "$WORKSPACE/www/js"
                uglifyjs --timings init.js -o ../min/custom-min.js
            
                cd "$WORKSPACE"
                tar --exclude='./www/css' --exclude='./www/js' -c -z -f archive.tgz ./www/

                '''       
            }
        }

        stage('SonarQube') {
            def scannerHome = tool 'sonarqube-scanner-3.3'
            def nodeHome = tool name: 'Node10', type: 'nodejs'
            withEnv(["PATH+NODE=${nodeHome}:${nodeHome}/bin"]) {
                withSonarQubeEnv('sonar-default') {
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectName=MDT-test -Dsonar.projectKey=mdt -Dsonar.sources=www"
                }
            }
            waitForQualityGate abortPipeline: true
            }
            
        stage('Archive') {
            archiveArtifacts 'archive.tgz'
        }
    }

    catch (err) {
        
        currentBuild.result = "FAILURE"

        throw err
    }
}
