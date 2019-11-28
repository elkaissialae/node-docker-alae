pipeline {
    agent any
     stages{
         stage('clone'){
             steps{
                sh "sudo rm -rf *"
                sh "sudo git clone https://labtest.francecentral.cloudapp.azure.com/administrator/node-docker-demo.git"
             }
         }
         stage('build'){
             steps{
                 sh "sudo docker build -t node-docker ."
             }
         }
         stage('run'){
             steps{
                sh "cd node-docker-demo && docker run -d --rm -p 3333:5432 -e POSTGRES_USER=admin -v \$(pwd)/.data:/var/lib/postgresql/data -v \$(pwd)/sql:/sql --name nd-db-hamza postgres:9.6"
                sh "sudo docker exec nd-db psql admin admin -f sql/migrations.sql"
                sh "sudo docker exec nd-db psql admin admin -f sql/seeds.sql"
                 
             }
         }
         stage('run_app'){
             steps{
                sh "sudo docker run --rm -p 3535:3000 -d -e DB_PORT=3333 -v \$(pwd)/app:/src/app -v \$(pwd)/public:/src/public --link nd-db-hamza --name nd-app-hamza node-docker"
                 
             }
         }
         stage('test'){
             steps{
                sh "sudo docker ps"
                sh "curl http://localhost:3535/"
                 
             }
         }
     }   
}