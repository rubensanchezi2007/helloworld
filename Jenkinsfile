pipeline {
    agent any

    stages {
        stage('GetCode') {
            steps {
               git 'https://github.com/rubensanchezi2007/helloworld'
            }
        }
        stage('Build') {
            steps {
               echo 'NO HAY QUE COMPILAR NADA ESTO ES PYTHON'
               bat "echo %WORKSPACE%"
            }
            
        }
        stage ('Tests')
        {
            parallel {
                stage('Unit') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult:'FAILURE'){
                            bat '''
                                SET PYTHONPATH=%WORKSPACE%
                                python -m pytest --junitxml=result-unit.xml test\\unit
                            '''
                        }
                    }
                    
                }
                stage('rest') {
                    steps {
                       catchError(buildResult: 'UNSTABLE', stageResult:'FAILURE'){
                            bat '''
                                SET FLASK_APP=app\\api.py
                                start python -m flask run
                                start java -jar c:\\Workspace\\wiremock-standalone-3.10.0.jar --port 9090 --root-dir=.\\test\\wiremock
                                SET PYTHONPATH=%WORKSPACE%
                                python -m pytest --junitxml=result-rest.xml test\\rest
                            '''
                       }
                    }
                }
            }
            
        }
        stage('Results') {
            steps {
               junit 'result*.xml'
            }
            
        }

    }
}