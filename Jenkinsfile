pipeline {
    agent any

    environment {
        IMAGE_NAME = "student-management:latest" // image locale dans Minikube
        MINIKUBE_NAMESPACE = "devops"
        PROJECT_DIR = "/var/lib/jenkins/workspace/williams_ditsougou_naura_5sleam1/Projet-5ème/student-management/student-management"
        SPRING_DEPLOYMENT_FILE = "/home/vagrant/spring-deployment.yaml"
        MYSQL_DEPLOYMENT_FILE = "/home/vagrant/mysql-deployment.yaml"
        KUBECONFIG = "/var/lib/jenkins/.kube/config" // kubeconfig copié pour Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'update-student-management', 
                    url: 'https://github.com/naura12345/williams_ditsougou_naura_5sleam1.git'
            }
        }

        stage('Build Maven') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh 'chmod +x mvnw'
                    sh './mvnw clean install -DskipTests'
                }
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                sh """
                    #!/bin/bash
                    set -e

                    echo "Activation du Docker de Minikube..."
                    eval \$(minikube docker-env --shell bash) || true

                    # Créer Dockerfile si besoin
                    DOCKERFILE="${PROJECT_DIR}/Dockerfile"
                    if [ ! -f "\$DOCKERFILE" ]; then
                        cat > "\$DOCKERFILE" <<EOF
FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
ARG JAR_FILE=target/*.jar
COPY \${JAR_FILE} app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.jar"]
EOF
                    fi

                    echo "Construction de l'image Docker dans Minikube..."
                    docker build -t ${IMAGE_NAME} ${PROJECT_DIR}

                    echo "Image Docker construite avec succès!"
                """
            }
        }
        stage('SonarQube Analysis') {
    steps {
        dir("${PROJECT_DIR}") {
            withSonarQubeEnv('SonarQube') { // le nom défini dans Jenkins
                sh """
                    ./mvnw sonar:sonar \
                        -Dsonar.projectKey=student-management \
                        -Dsonar.projectName='Student Management'
                """
            }
        }
    }
}

        stage('Deploy MySQL') {
            steps {
                sh """
                    echo "Déploiement MySQL sur Minikube..."
                    kubectl --kubeconfig=${KUBECONFIG} apply --validate=false -f ${MYSQL_DEPLOYMENT_FILE} -n ${MINIKUBE_NAMESPACE}
                    kubectl --kubeconfig=${KUBECONFIG} rollout status deployment/mysql -n ${MINIKUBE_NAMESPACE}
                """
            }
        }

        stage('Deploy Spring Boot') {
            steps {
                sh """
                    echo "Déploiement Spring Boot sur Minikube..."
                    kubectl --kubeconfig=${KUBECONFIG} apply --validate=false -f ${SPRING_DEPLOYMENT_FILE} -n ${MINIKUBE_NAMESPACE}
                    kubectl --kubeconfig=${KUBECONFIG} rollout status deployment/spring-deployment -n ${MINIKUBE_NAMESPACE}
                """
            }
        }

        stage('Port Forward') {
            steps {
                sh "kubectl --kubeconfig=${KUBECONFIG} port-forward svc/spring-service 8082:8080 -n ${MINIKUBE_NAMESPACE} &"
            }
        }
    }

    post {
        success {
            echo 'Pipeline terminé avec succès !'
        }
        failure {
            echo 'Le pipeline a échoué.'
        }
    }
}
