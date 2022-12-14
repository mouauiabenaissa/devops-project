pipeline {
  agent any
  environment {
    NEXUS_VERSION = "nexus3"
    NEXUS_PROTOCOL = "http"
    NEXUS_URL = "http://localhost:8081"
    NEXUS_REPOSITORY = "nexus_spring/"
    NEXUS_CREDENTIAL_ID = "Nexus-Creds"
    DOCKER_CREDENTIAL_ID = "Docker-Creds"
    VERSION = "1.${env.BUILD_NUMBER}"
    DOCKER_CREDS = credentials('Docker-Creds')
  }

  stages {

    stage("Maven Clean") {
      steps {
        script {
          sh "mvn -f'Spring/pom.xml' clean -DskipTests=true -Drevision=${VERSION}"
        }
      }
    }
    stage("Maven Compile") {
      steps {
        script {
          sh "mvn -f'Spring/pom.xml' compile -DskipTests=true -Drevision=${VERSION}"
        }
      }
    }
    stage("Maven test") {
      steps {
        script {
          sh "mvn -f'Spring/pom.xml' test -Drevision=${VERSION}"
        }
      }
    }
    /*stage("Maven Sonarqube") {
      steps {
        script {
          sh "mvn -f'Spring/pom.xml' sonar:sonar -Dsonar.login=admin -Dsonar.password=Admin -Drevision=${VERSION}"
        }
      }
    }*/
    stage("Maven Build") {
      steps {
        script {
          sh "mvn -f'Spring/pom.xml' package -DskipTests=true -Drevision=${VERSION}"
        }
        echo ":$BUILD_NUMBER"
      }
    }
    stage("Publish to Nexus Repository Manager") {
      steps {
        script {
          echo "nexus here"
          pom = readMavenPom file: "Spring/pom.xml";
          echo "${pom}"
          filesByGlob = findFiles(glob: "Spring/target/*.${pom.packaging}");
          echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
          artifactPath = filesByGlob[0].path;
          artifactExists = fileExists artifactPath;
          if (artifactExists) {
            echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${VERSION}";
            nexusArtifactUploader(
              nexusVersion: NEXUS_VERSION,
              credentialsId: '1ef67b53-da11-4351-9d8f-6adf35baeae2', 
              protocol: NEXUS_PROTOCOL,
              groupId: pom.groupId,
              version: VERSION,
              repository: NEXUS_REPOSITORY,
              artifacts: [
                [artifactId: pom.artifactId,
                  classifier: '',
                  file: artifactPath,
                  type: pom.packaging
                ]
              ]
            );
          } else {
            error "*** File: ${artifactPath}, could not be found";
          }
        }
      }
    }
    /*
    stage('Pull the file off Nexus') {
      steps {
        dir('Spring') {
          withCredentials([usernameColonPassword(credentialsId: 'Nexus-Creds', variable: 'NEXUS_CREDENTIALS')]) {
            sh script: 'curl -u ${NEXUS_CREDENTIALS} -o ./target/tpachat.jar "$NEXUS_URL/repository/$NEXUS_REPOSITORY/com/esprit/examen/tpAchatProject/$VERSION/tpAchatProject-$VERSION.jar"'
          }
        }
      }
    }
    stage('Building Docker Image Angular') {
      steps {
        dir('Angular/crud-tuto-front') {
          sh 'docker build -t $DOCKER_CREDS_USR/projet-devops-front .'
        }
      }
    }
    stage('Login to DockerHub') {
      steps {
        dir('Spring') {
          echo DOCKER_CREDS_USR
          sh('docker login -u $DOCKER_CREDS_USR -p $DOCKER_CREDS_PSW')
        }
      }
    }
    stage('Push to DockerHub Angular') {
      steps {
        dir('Spring') {
          sh 'docker push $DOCKER_CREDS_USR/projet-devops-front'
        }
      }
    }
    stage('Docker Compose') {
      steps {
        sh 'docker-compose up -d'
      }
    }*/
  }

}
