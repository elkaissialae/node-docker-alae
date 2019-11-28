pipeline {
     agent any
     stages{
         stage('clone'){
             steps{
                sh "sudo rm -rf *"
                sh "sudo git clone --branch ali-node https://labtest.francecentral.cloudapp.azure.com/administrator/node-docker-demo.git"
             }
         }
         
         stage('docker'){
             steps{
                 sh "cd node-docker-demo && docker build -t node-docker ."
             }
         }

         stage('launch database'){
             steps {
                 sh "cd node-docker-demo && docker run -d --rm -p 5450:5432 -e POSTGRES_USER=admin -v \$(pwd)/.data:/var/lib/postgresql/data -v \$(pwd)/sql:/sql --name nd-dbali postgres:9.6"
             }
         }

         stage('execute database shema script'){
             steps{
                 sh "docker ps -a && docker exec nd-dbali psql admin admin -f /sql/migrations.sql"
             }
         }

         stage('execute database data script'){
             steps{
                 sh "docker exec nd-dbali psql admin admin -f /sql/seeds.sql"
             }
         }

         stage('run'){
             steps{
                sh "docker run --rm -p 3000:3000 -e DB_PORT=5450 -d -v \$(pwd)/app:/src/app -v \$(pwd)/public:/src/public --link nd-dbali --name nd-app node-docker"
             }
         }
         
         stage('test'){
             steps{
                sh "sleep 15 && curl http://localhost:3000/"
             }
         }
     }   
}