pipeline {
     agent any
     stages{

         stage('clean docker'){
             steps{
                 sh "sudo docker rm -f nd-appali || true"
                 sh "sudo docker rm -f nd-dbali1 || true"
             }
         }

         stage('clone'){
             steps{
                sh "sudo rm -rf *"
                sh "sudo git clone --branch ali-node https://labtest.francecentral.cloudapp.azure.com/administrator/node-docker-demo.git"
             }
         }
         
         stage('docker'){
             steps{
                 sh "cd node-docker-demo && sudo docker build -t node-docker-ali ."
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
                sh "sudo docker run --rm -p 3303:3000 -e DB_PORT=5451 -d -v \$(pwd)/app:/src/app -v \$(pwd)/public:/src/public --link nd-dbali1 --name nd-appali node-docker-ali"
             }
         }
         
         stage('test'){
             steps{
                sh "sleep 5 && docker ps -a && curl http://localhost:3303/"
             }
         }

         stage('clean docker post'){
             steps{
                 sh "sudo docker stop nd-appali || true"
                 sh "sudo docker stop nd-dbali1 || true"
                 sh "sudo rm -rf *"
             }
         }

         
     }   
}