# Exercício 1: mongo:4.2 e mongo-express

# Parâmetro de execução de container (docker run)
```bash
    docker run -d --name postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 postgres
    docker run -d --name pgadmin -e PGADMIN_DEFAULT_EMAIL=admin@admin.com -e PGADMIN_DEFAULT_PASSWORD=admin -p 80:80 --link postgres:db dpage/pgadmin4

```

# Descrição do docker-compose.yml
```yaml

version: '3.8'

services:
  postgres:
    image: postgres
    container_name: postgres
    environment:
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "80:80"
    depends_on:
      - postgres

```

# Descrição do pipeline Jenkins

```groovy
pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/dutra-felipe/Avaliacao_DevOps.git'
            }
        }

        stage('Build and Deploy') {
            steps {
                script {
                    sh 'docker-compose --version'
                    
                    dir('./parte3-dpage_pg4') {
                        sh 'docker-compose down'
                        sh 'docker-compose up -d'
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                dir('./parte3-dpage_pg4') {
                    //sh 'docker-compose down'
                }
            }
        }
    }
}

```