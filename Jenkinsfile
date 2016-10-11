
def flow
node('master') {
    git branch: 'master', changelog: false, poll: true, url: 'https://github.com/gb1234/workflow-plugin-pipeline-demo'
    flow = load 'flow.groovy'
    flow.devQAStaging()
}
flow.production()
