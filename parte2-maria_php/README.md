# Exercício 1: mongo:4.2 e mongo-express

# Parâmetro de execução de container (docker run)
```bash
docker run -d --name mariadb -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mariadb
docker run -d --name phpmyadmin -e PMA_HOST=mariadb -p 8081:80 --link mariadb:db phpmyadmin/phpmyadmin

```

# Descrição do docker-compose.yml
```yaml

version: '3.8'

services:
  mariadb:
    image: mariadb
    container_name: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3306:3306"

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    environment:
      PMA_HOST: mariadb
    ports:
      - "8081:80"
    depends_on:
      - mariadb



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
                    
                    dir('./parte2-maria_php') {
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
                dir('./parte2-maria_php') {
                    //sh 'docker-compose down'
                }
            }
        }
    }
}


```