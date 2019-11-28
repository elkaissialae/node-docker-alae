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
                 sh "cd node-docker-demo && sudo docker build -t node-docker ."
             }
         }

         stage('launch database'){
             steps {
                 sh "cd node-docker-demo && sudo docker run -d --rm -p 5451:5432 -e POSTGRES_USER=admin -v \$(pwd)/.data:/var/lib/postgresql/data -v \$(pwd)/sql:/sql --name nd-dbali1 postgres:9.6"
             }
         }

         stage('execute database shema script'){
             steps{
                 sh "sleep 10 && docker ps -a && sudo docker exec nd-dbali1 psql admin admin -f /sql/migrations.sql"
             }
         }

         stage('execute database data script'){
             steps{
                 sh "sudo docker exec nd-dbali1 psql admin admin -f /sql/seeds.sql"
             }
         }

         stage('run'){
             steps{
                sh "sudo docker run --rm -p 3303:3000 -e DB_PORT=5451 -d -v \$(pwd)/app:/src/app -v \$(pwd)/public:/src/public --link nd-dbali1 --name nd-appali node-docker"
             }
         }
         
         stage('test'){
             steps{
                sh "sleep 15 && curl http://localhost:3303/"
             }
         }
     }   
}