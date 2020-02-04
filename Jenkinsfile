node{
    
    stage("git clone"){
        git 'https://github.com/Naveelh/spring-boot-mongo-docker.git'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven-3.6.1", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    }
    
    stage("ExecuteSoanrQubeReport"){
      def mavenHome =  tool name: "Maven-3.6.1", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh  "${mavenHome}/bin/mvn sonar:sonar"
    }  
 
    /* stage("build & SonarQube analysis") {
          node {
              withSonarQubeEnv('sonar') {
                 sh "/opt/maven/bin/mvn clean verify sonar:sonar -Dmaven.test.skip=true"
              }
          }
      } */
    
    stage('Build Docker Image'){
        sh 'docker build -t naveelh/spring-boot-mongo .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'DOCKER_HUB_CREDENTIALS', variable: 'DOCKER_HUB_CREDENTIALS')]) {
          sh "docker login -u naveelh -p ${DOCKER_HUB_CREDENTIALS}"
        }
        sh 'docker push naveelh/spring-boot-mongo'
     }
     
    stage("Deploy To Kuberates Cluster"){
       kubernetesDeploy(
         configs: 'springBootMongo.yml', 
         kubeconfigId: 'KUBERNETES_CLUSTER_CONFIG',
         enableConfigSubstitution: true
        )
     }
     
    stage("Nexus upload"){
        nexusArtifactUploader artifacts: [[artifactId: 'spring-boot-mongo', classifier: '', file: '/var/lib/jenkins/workspace/k8-mith/target/spring-boot-mongo-1.0.jar', type: 'jar']], credentialsId: 'nexus', groupId: 'com.mt', nexusUrl: '52.66.245.27:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'SAMPLE1', version: '1.0'
    }
      
}
