pipeline {
	agent any

	options {
		buildDiscarder(logRotator(numToKeepStr: '05'))
	}

	parameters {
		booleanParam name: 'RUN_TESTS', defaultValue: true, description: 'Run Tests?'
		booleanParam name: 'RUN_ANALYSIS', defaultValue: true, description: 'Run Static Code Analysis?'
		booleanParam name: 'DEPLOY', defaultValue: true, description: 'Deploy Artifacts?'
	}

	stages {
        stage('Build') {
            steps {
                cmake arguments: '-DCMAKE_TOOLCHAIN_FILE=~/var/lib/jenkins/workspace/C-Project-Pipeline/vcpkg/scripts/buildsystems/vcpkg.cmake', installation: 'Cmake'
                cmakeBuild buildType: 'Release', cleanBuild: true, installation: 'Cmake', steps: [[withCmake: true]]
            }
        }

        stage('Test') {
            when {
                environment name: 'RUN_TESTS', value: 'true'
            }
            steps {
                ctest 'InSearchPath'
            }
        }

        stage('Analyse') {
            when {
                environment name: 'RUN_ANALYSIS', value: 'true'
            }
            steps {
                sh label: '', returnStatus: true, script: 'cppcheck . --xml --language=c++ --suppressions-list=suppressions.txt 2> cppcheck-result.xml'
                publishCppcheck allowNoReport: true, ignoreBlankFiles: true, pattern: '**/cppcheck-result.xml'
            }
        }

        stage('Deploy') {
            when {
                environment name: 'DEPLOY', value: 'true'
            }
            steps {
                sh label: '', returnStatus: true, script: '''cp jenkinsexample ~
                cp test/testPro ~'''
            }
        }
	}
}
