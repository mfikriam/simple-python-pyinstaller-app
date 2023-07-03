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
        docker.image('python:2-alpine').inside {
            sh 'apk add --no-cache py-pip'
            sh 'pip install pyinstaller'
            sh 'pyinstaller --onefile sources/add2vals.py'
        }
        post {
            success {
                archiveArtifacts 'dist/add2vals'
            }
        }
    }
}