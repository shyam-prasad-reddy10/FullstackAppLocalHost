pipeline {
    agent any

    tools {
        jdk 'JDK_HOME'        // same as in your Jenkins tool config
        maven 'MAVEN_HOME'    // same as in your Jenkins tool config
    }

    environment {
        BACKEND_DIR   = 'crud_backend\\crud_backend-main'
        FRONTEND_DIR  = 'crud_frontend\\crud_frontend-main'

        TOMCAT_WEBAPPS = 'C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\webapps'

        BACKEND_WAR   = 'springapp1.war'
        FRONTEND_WAR  = 'frontapp1.war'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/shyam-prasad-reddy10/FullstackAppLocalHost.git', branch: 'main'
            }
        }

        stage('Build Frontend (Vite)') {
            steps {
                dir("${env.FRONTEND_DIR}") {
                    script {
                        def nodeHome = tool name: 'NODE_HOME', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                        env.PATH = "${nodeHome}\\;${nodeHome}\\bin;${env.PATH}"
                    }
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Package Frontend as WAR') {
            steps {
                dir("${env.FRONTEND_DIR}") {
                    bat """
                        if exist frontapp1_war rmdir /S /Q frontapp1_war
                        mkdir frontapp1_war\\WEB-INF
                        xcopy /E /I /Y dist frontapp1_war
                        jar -cvf ..\\..\\${FRONTEND_WAR} -C frontapp1_war .
                    """
                }
            }
        }

        stage('Build Backend (Spring Boot WAR)') {
            steps {
                dir("${env.BACKEND_DIR}") {
                    bat 'mvn clean package -DskipTests'
                    bat "copy /Y target\\*.war ..\\..\\${BACKEND_WAR}"
                }
            }
        }

        stage('Deploy WARs to Tomcat webapps') {
            steps {
                bat """
                    copy /Y ${BACKEND_WAR} "${TOMCAT_WEBAPPS}\\${BACKEND_WAR}"
                    copy /Y ${FRONTEND_WAR} "${TOMCAT_WEBAPPS}\\${FRONTEND_WAR}"
                """
            }
        }
    }

    post {
        success {
            echo "✅ Backend deployed: http://localhost:9090/springapp1"
            echo "✅ Frontend deployed: http://localhost:9090/frontapp1"
        }
        failure {
            echo "❌ Build or deployment failed"
        }
    }
}
