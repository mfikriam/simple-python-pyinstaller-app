node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --junitxml=test-reports/results.xml sources/test_calc.py'
        }
        step([$class: 'JUnitResultArchiver', testResults: 'test-reports/results.xml'])
    }

    stage('Deliver') {
        dir(path: env.BUILD_ID) {
            unstash('compiled-results')
            sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} pyinstaller -F add2vals.py"
        }
        post {
            success {
                archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                sh "docker run --rm -v ${env.VOLUME} ${env.IMAGE} rm -rf build dist"
            }
        }
    }
}