
def flow
node('master') {
    git branch: 'master', changelog: false, poll: true, url: 'https://github.com/lavaliere/workflow-plugin-pipeline-demo.git'
    flow = load 'flow.groovy'
    flow.devQAStaging()
}
flow.production()
