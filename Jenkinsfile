pipeline {
    agent {label 'ec2_slave'}
    environment {
        rds_hostname_Endpoint = 'terraform-20220521182115374600000001.cs9ejbrf2btj.us-east-1.rds.amazonaws.com'
        redis_hostname_Endpoint    = 'redis-cluster.jebxv2.0001.use1.cache.amazonaws.com'
        }
    stages {
        stage('CI') {
            steps {
                // Build the Dockerfile
                sh "docker build . -t moutazmuhammad/node_app_ci_cd"

                // First login to docker
                withCredentials([usernamePassword(credentialsId: 'docker_hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){

                    sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                }

                // Push the image
                sh "docker push moutazmuhammad/node_app_ci_cd"
                
            }
        }
        stage('CD') {
            steps {
            withCredentials([usernamePassword(credentialsId: 'RDS_CRED', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                // Run the docker container 
                sh "docker run -d -it -p 3000:3000 --env RDS_HOSTNAME=${rds_hostname_Endpoint} --env RDS_USERNAME=${USERNAME} --env RDS_PASSWORD=${PASSWORD} --env REDIS_HOSTNAME=${redis_hostname_Endpoint} --env RDS_PORT=3306 moutazmuhammad/node_app_ci_cd"
            }
                
            }
        }
    }
}
