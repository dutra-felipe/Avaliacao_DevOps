# Exercício 1: mongo:4.2 e mongo-express

# Parâmetro de execução de container (docker run)
```bash
	docker run -d --name mongo -p 27017:27017 mongo:4.2
	docker run -d --name mongo-express -p 8081:8081 --link mongo:mongo mongo-express

```

# Descrição do docker-compose.yml
```yaml

version: '3.8'

services:
  mongo:
    image: mongo:4.2
    container_name: mongo
    ports:
      - "27017:27017"

  mongo-express:
    image: mongo-express
    container_name: mongo-express
    ports:
      - "8081:8081"
    environment:
      - ME_CONFIG_MONGODB_SERVER=mongo
    depends_on:
      - mongo


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
                    
                    dir('Avaliacao_DevOps/parte1-mongo') {
                        sh 'docker-compose up -d'
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                dir('Avaliacao_DevOps/parte1-mongo') {
                    sh 'docker-compose down'
                }
            }
        }
    }
}

```