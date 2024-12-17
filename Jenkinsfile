pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-docker-image:latest'
        DOCKERHUB_USER = 'dockerhub-username'
        DOCKERHUB_CRED = 'dockerhub-creds'
        ANSIBLE_SERVER = 'user@ansible-server'
        K8S_SERVER = 'user@k8s-server'
    }

    stages {

        // Stage 1: Checkout Kode Sumber
        stage('Checkout Kode Sumber') {
            steps {
                echo 'Mengambil kode sumber dari Git...'
                git branch: 'main', url: 'https://github.com/your-repository.git'
            }
        }

        // Stage 2: Mengirim File
        stage('Mengirim File') {
            steps {
                echo 'Mengirim file ke server Ansible...'
                sh ''
                scp Dockerfile ${ANSIBLE_SERVER}:/home/user/Dockerfile
                '''
            }
        }

        // Stage 3: Build Image Docker
        stage('Build Image Docker') {
            steps {
                echo 'Membangun image Docker...'
                sh ''
                docker build -t ${DOCKERHUB_USER}/${DOCKER_IMAGE} .
                '''
            }
        }

        // Stage 4: Push ke DockerHub
        stage('Push ke DockerHub') {
            steps {
                echo 'Push image Docker ke DockerHub...'
                withCredentials([string(credentialsId: "${DOCKERHUB_CRED}", variable: 'DOCKERHUB_PASS')]) {
                    sh '''
                    docker login -u ${DOCKERHUB_USER} -p ${DOCKERHUB_PASS}
                    docker push ${DOCKERHUB_USER}/${DOCKER_IMAGE}
                    '''
                }
            }
        }

        // Stage 5: Copy File ke Server Kubernetes
        stage('Copy File ke Server Kubernetes') {
            steps {
                echo 'Mengirim file konfigurasi Kubernetes...'
                sh ''
                scp k8s-config.yaml ${K8S_SERVER}:/home/user/k8s-config.yaml
                '''
            }
        }

        // Stage 6: Deployment Menggunakan Ansible
        stage('Deployment Menggunakan Ansible') {
            steps {
                echo 'Melakukan deployment ke Kubernetes menggunakan Ansible...'
                sh ''
                ssh ${ANSIBLE_SERVER} "ansible-playbook -i /home/user/inventory /home/user/k8s-deployment.yaml"
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline berhasil dijalankan!'
        }
        failure {
            echo 'Pipeline gagal, periksa log untuk detail kesalahan!'
        }
    }
}
