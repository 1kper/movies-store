def imageName = 'mlabouardy/movies-store'
def registry = 'https://registry.slowcoder.com'

node('workers'){
    stage('Checkout'){
        checkout scm
    }

    def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")

    stage('Tests'){
        parallel(
            'Quality Tests': {
                sh "docker run --rm ${imageName}-test npm run lint"
            },
            'Integration Tests': {
                sh "docker run --rm ${imageName}-test npm run test"
            },
            'Coverage Reports': {
                sh "docker run --rm -v $PWD/coverage:/app/coverage ${imageName}-test npm run coverage-html"
                publishHTML (target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: "$PWD/coverage",
                    reportFiles: "index.html",
                    reportName: "Coverage Report"
                ])
            }
        )
    }

    stage('Build'){
        
    }

    stage('Push'){
       sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 635154829813.dkr.ecr.us-west-2.amazonaws.com"
        sh "docker build -t 1kper/movies-store ."
        sh "docker tag 1kper/movies-store:latest 635154829813.dkr.ecr.us-west-2.amazonaws.com/1kper/movies-store:latest"
        sh "docker push 635154829813.dkr.ecr.us-west-2.amazonaws.com/1kper/movies-store:latest"
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}
