node {
    stage ('Checkout') {
        git credentialsId: 'gh-token-demo', url: 'https://github.com/dbielik/mdt'
    }
    stage ('Build') {
        nodejs('Node10-2') {
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
    stage('Archive'){
        archiveArtifacts 'archive.tgz'
    }
}
