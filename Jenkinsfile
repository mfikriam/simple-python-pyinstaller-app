def build() {
    sh 'python -m py_compile sources/add2vals.py sources/calc.py'
    stash(name: 'compiled-results', includes: 'sources/*.py*')
}

def test() {
    sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
}

def deliver() {
    dir(path: env.BUILD_ID) {
        unstash(name: 'compiled-results')
        sh "docker run --rm -v $(pwd)/sources:/src cdrx/pyinstaller-linux:python2 'pyinstaller -F add2vals.py'"
    }

    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
    sh "docker run --rm -v $(pwd)/sources:/src cdrx/pyinstaller-linux:python2 'rm -rf build dist'"
}

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                build()
            }
        }
        stage('Test') {
            steps {
                test()
            }
        }
        stage('Deliver') {
            steps {
                deliver()
            }
        }
    }
}