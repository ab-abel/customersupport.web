pipeline {
  agent any

    stages{
        stage('Clean-up volumes') {
            steps {
                sh 'docker system prune -a --volumes -f'
            }
        }
        stage('Build Container') {
            steps {
                sh 'docker compose up -d --no-color --wait'
                sh 'docker compose ps'
            }

        }
        stage('Run tests against the container') {
            steps {
                sh 'curl http://localhost:3000/param?query=demo | jq'
            }
        }

        stage('Deploy to Production') {
            input{
                message "Click OK! to deploy to Production?"
                ok "OK"
            }
            steps {
                sh 'ssh -o StrictHostKeyChecking=no deployment-user@52.203.249.167 "\
                        cd mallet; \
                        git pull origin master; \
                        docker compose down --remove-orphans;\
                        docker compose up; --no-warn-script-location; \
                        sudo systemctl restart nginx "'
            }
        }
    }
    post {
        always {
            sh 'docker compose down --remove-orphans -v'
            sh 'docker compose ps'
        }
    }
}