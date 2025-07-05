pipeline{
    agent any

    environment{
        DOCKER_IMAGE = 'apimonedastt'
        CONTAINER_NAME = 'dockerapimonedastt'
        DOCKER_NETWORK = 'dockermonedas_red'
        DOCKER_BUILD_DIR = 'presentacion'
        HOST_PORT = '9080'
        CONTAINER_PORT = '8080'
        PATH = '/usr/local/bin:$PATH'
    }

    stages{
        /*
        stage('Compilación Maven'){
            steps{
                sh 'mvn clean package -Dskiptests'
            }
        }*/

        stage('Check Docker Access') {
            steps {
                sh '''
                    export PATH=/usr/local/bin:$PATH
                    echo "Docker path:"
                    which docker
                    echo "Docker version:"
                    docker --version
                '''
            }
        }
        
        stage('Construir imagen'){
            steps{
                //dir("${DOCKER_BUILD_DIR}"){
                    sh "docker build . -t ${DOCKER_IMAGE}"
                //}
            }
        }
        

        stage('Limpiar contenedor existente') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                        sh """
                        docker container inspect ${CONTAINER_NAME} >nul 2>&1 && (
                            docker container stop ${CONTAINER_NAME}
                            docker container rm ${CONTAINER_NAME}
                        ) || echo "No existe el contenedor '${CONTAINER_NAME}'."
                        """
                    }
                }
            }
        }
        
        stage('Desplegar contenedor'){
            steps{
                sh "docker run --network ${DOCKER_NETWORK} --name ${CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} -d ${DOCKER_IMAGE}"
            }
        }
    }
}
