pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/FacuMiglio/bootcamp-devops-desafio6.git'
            }
        }
        stage('Check Apache') {
            steps {
                sh 'echo "Verificando instalación de Apache"'
                sshagent(['ssh-credential-id']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no jciccioli@192.168.100.99
                    '''}
                sh '''#!/bin/bash
                status=$(systemctl is-active apache2)
                    
 
 
                if ( "$status" == "active" ){
                    echo "Apache2 está corriendo."
                    }
                else {
                    echo "Apache2 no está corriendo."
                    echo "Instalando Apache2"
                    sudo apt update -y
                    sudo apt install apache2 -y
                    sudo systemctl stop apache2
                    sudo systemctl enable apache2
                }'''
  
                }
            }
        stage('Deploy to Apache') {
            steps {
                sh 'echo "<h1>Hello from Jenkins Pipeline 31/08/2024 22:46</h1> " '
            }
        }
    }
}
