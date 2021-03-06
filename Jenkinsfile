
jettyUrl = 'http://localhost:8081/'

def servers

stage('Dev') {
    node {
        checkout scm
        servers = load 'servers.groovy'
        mvn '-o clean package'
        dir('target') {stash name: 'war', includes: 'x.war'}
    }
}

stage('QA') {
    parallel(longerTests: {
        runTests(servers, 30)
    }, quickerTests: {
        runTests(servers, 20)
    })
}

milestone 1
stage('Staging') {
    lock(resource: 'staging-server', inversePrecedence: true) {
        milestone 2
        node {
            servers.deploy 'staging'
        }
        input message: "Does ${jettyUrl}staging/ look good?"
    }
    try {
        checkpoint('Before production')
    } catch (NoSuchMethodError _) {
        echo 'Checkpoint feature available in CloudBees Jenkins Enterprise.'
    }
}

milestone 3
stage ('Production') {
    lock(resource: 'production-server', inversePrecedence: true) {
        node {
            sh "wget -O - -S ${jettyUrl}staging/"
            echo 'Production server looks to be alive'
            servers.deploy 'production'
            echo "Deployed to ${jettyUrl}production/"
        }
    }
}
def mvn(args) {
    sh "${tool 'M3'}/bin/mvn ${args}"
}

def runTests(servers, duration) {
    node {
        checkout scm
        servers.runWithServer {id ->
            mvn "-o -f sometests test -Durl=${jettyUrl}${id}/ -Dduration=${duration}"
        }
        junit '**/target/surefire-reports/TEST-*.xml'
    }
}
