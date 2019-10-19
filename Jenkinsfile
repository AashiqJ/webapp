pipeline{
    agent{
        label "master"
    }
    tools{
        maven "Maven"
    }
    environment{
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "localhost:8081"
        NEXUS_REPOSITORY = "maven-releases"
        NEXUS_CREDENTIAL_ID = "4af1168e-1b44-46cd-8d00-56480d6407c0"
        TOMCAT_USER = "admin"
        TOMCAT_SERVER = "3.17.186.70"
    }
    stages{
        stage('Checkout'){
            steps{
                checkout scm
            }
        }

        stage("mvn build"){
            steps{
                script{
                    bat(/"${MAVEN_HOME}\bin\mvn" package/)
                }
            }
        }

        stage("publish to nexus"){
            steps{
                script{
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}";
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists){
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging:  ${pom.packaging}"
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: '${BUILD_NUMBER}',
                            repository: NEXUS_REPOSITORY,
                            credentialsId:NEXUS_CREDENTIAL_ID,
                            artifacts: [
                               [artifactId:pom.artifactId,
                               classifier: '',
                               file: artifactPath,
                               type: pom.packaging],
                               [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"] 
                            ]
                        );
                    }else{
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
        stage("Deploy"){
            steps{
                script{
                     bat("C:\\Users\\aashiq.jacob\\Downloads\\curl-7.66.0_2-win64-mingw\\curl-7.66.0-win64-mingw\\bin\\curl --upload-file target/mvn-hello-world.war http://${TOMCAT_USER}:${env.TOMCAT_PASSWORD}@${TOMCAT_SERVER}:8080/manager/deploy?path=/mvn-hello-world") 
                }
            }
        }
    }
}
