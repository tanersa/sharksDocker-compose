# Docker - Docker Compose


![alt text](https://github.com/tanersa/sharksJenkins/blob/master/Docker.png)

<br />

 **DOCKER**
 
 **Docker** is a container runtime that is **not SERVERLESS**. Therefore, Docker requires to be managed by developers during building and running applications. 
 In reality, there are still servers being used during leveraging Docker but they are abstracted away from app development. 
 Example of some serverless services are **Kubernetes, AWS Lambda**, and **MS Azure functions**. 
 
 <br />
 
 **The most benefit of using docker:**
 
   -  Easy to migrate to different environments.
   -  Create your container, complete your test, and remove container in a short period of time in comparison with Virtulization.
   -  Better microservices, better isolation which will help us to achieve High Availability (HA).
   -  Can be used docker in local machine after insatlling docker desktop. Therefore, you would be able to see containers on dashboard. 
   -  Containers even can be removed from docker desktop dashboard itself or check images and more.
 
 <br />
  
 **CI/CD PIPELINE USING DOCKER**
 <br />
 Looking at the above image, we can visualize our pipeline while leveraging **Docker** and **Jenkins**. 
 
   -  Once developers **check-in** their changes to remote repo using **git**.
   -  Jenkins will **continously build** the project using **maven** which will create **artifact** (executables). 
   -  In another words, Jenkins will pull the changes from remote repo as soon as push occurs then will build the war 
      file deployment for the project.
   -  Then, artifact will be deployed  on **Docker container.**

 <br />

**STEPS TO CONFIGURE DOCKER ON AWS**

**Create instance of Docker on AWS**

**Change the hostname for the machine**

     hostname docker-host
     
**Exit from the machine and log back in to machine**

     exit
     sudi su -
     reboot
     (ssh to instance one more time)
     sudo su -
     vim /etc/hostname
     (Add text in hostname file as "docker-host")
     reboot
     (ssh to instance one more time)
     
Then, you should be able to see **docker-host** as your hostname     

**To configure docker on your linux machine**

     yum install docker -y 
     amazon-linux-extras install docker

**Run below commands to list docker containers:**

     docker ps    OR     docker container ls
     
**To check whether docker started and start docker if needed**

     service docker status 
     
     if INACTIVE, then run:
     service docker start
  
**Build docker containers from images**

Docker containers are build from  **docker images**. It's same as **VMs** are created on top of Amazon Machine Images **(AMIs)**

**To check docker images**

     docker images
     
**To pull docker image**

Let's say, we would like to create **Tomcat** container. 
   
   -  Go to your Dockerhub account and search for Tomcat image. 
   -  After finding Tomcat version that you would like to pull, copy the pull command on the page.
   
**To pull tomcat image**

     docker pull tomcat
     
**To check docker images**

     docker images
     
**What we do here?**

We have Tomcat application and we are **Dockerizing** it or **Containerizing** it.

**To create Tomcat container**

     docker run --name tomcat-container -p 8080:8080 tomcat-latest
     Here, latest is our tag.
     
     This cmd will run docker on FOREGROUND MODE.
     
     In order to run this container all the time in background, we call it DETACH MODE. Do following...
     
     docker run -d --name tomcat-container -p 8080:8080 tomcat-latest
     
**To go to inside your container**

     docker exec -it tomcat-container /bin/bash
     
     
**List all files in docker continer and go to webapps directory

     ls
     cd webapps
     (Nothing found)
     cd ..
     cd webapps..dist/
     ls
     (All files here)
     (Copy everything here and paste in webapps directory)
     cp -R ../webapps
     
**Paste your public ip for the machine and add port no 8080**

     TOMCAT TEST APP should be on the screen.
     
**Note**: 

      In order to remove containers:

      docker stop tomcat-container
      docker rm tomcat-container
      
      To see exited containers:
      
      docker ps -a
      
      To remove docker images:
      
      docker rmi {image_id/image_name-tag}
      
      To build custom image:
      
      docker build -t {name-of-image} .
      
 <br />
 
**DOCKER COMPOSE**      

We'll use **docker compose** in order to **orchestrate** docker containers in lower environments. 

Use yaml file to configure your applications' services. You would be able to create and start all services with a single command from your configuration file.
In comparison with other container orchestration tool **K8s**, **docker compose** is less complicated. Containers can be destroyed with just command.
      
Let's find a solution using **Docker-Compose**...

   -  Create instance for docker compose on AWS Console ( For instance, centos7 instance which is from Linux family).
   -  ssh to your centos7 instance.
   -  Install docker on your centos7 instance (Each machine has its own pre-requisites)
       
          sudo yum install -y yum-utils
          sudo yum-config-manager     --add-repo     
          sudo yum install -y docker-ce
          sudo systemctl start docker
          sudo systemctl enable docker
          sudo systemctl status docker
         
   -  Check the users if there is centos user  
       
          cat /etc/passwd
          (No centos user)
          
   -  Check the docker if exists

           cat /etc/group
           (docker group exists)
           
   -  Check if centos is part of docker group

            id centos
            (centos is not part of docker group)
            
   -  To add centos user to docker group
       
            usermod -aG docker centos
            id centos
            (centos is in docker group now)
            
            
   -  To see containers

            docker ps
            exit
            ssh to your instance ( ssh -i ~/Downloads/demo.pem centos@{public-ip-of-instance} )
            doccker ps
            
   -  Install docker compose for centos7 linux family
   
            sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  
   -  Make docker compose executable

            sudo chmod +x /usr/local/bin/docker-compose
            docker-compose
            (You should get some output for installation of docker compose)
            
   -  In order to debug with docker compose

            docker info
            docker info | grep  -i root
            
<br />            
            
**Note:** 
     
**Docker Root Directory** ---> /var/lib/docker
   
   Whenever images are pulled and container is created it takes some memory from the disk space. Then all those are written to above directory. 
   
   -  To see how big is the disk usage under docker directory:
  
            sudo du -sh /var/lib/docker
            
   -  To see max size of the storage
   
            df -h
            
**Note:** 
    Let's say we have 2.3 GB and 99% of that memory is already used. Then, we have to create **voume** and **mount** this volume to your file system.
    That would increase your memory.
    
   -  Let's pull jenkins image from docker hub now.

            docker pull jenkins/jenkins:lts
            
            This image will also take some space from the memory under /var/lib/docker directory.
            
   -  You might need to create a new directory for data. That would be named as jenkins-data. 
   
            /home/centos/
            cd jenkins-data
            
            Then we are in jenkins-data folder
            
   -  Create one more directory under **jenkins-data** called **jenkins-home**

             mkdir jenkins-home

**Note:** 
    
 The reason why we created this **jenkins-home** directory. Whenever we run jenkins jobs, it will genrate some sort of data. 
 By default, container itself is **ephemeral** (stateless). We would create, delete and recreate but it is **not persistent voume**.
 Therefore, we can store those jobs generated data somewhere. Basically, under a new directory we may create. 
 
   -  Consequently, we need to give centos user absolute right for jenkins-home directory.
    
            sudo chown 1000:1000 -R jenkins-data/jenkins-home
          
   -  Now, let's create our docker-compose yaml file to configure services.

            version: '3'
            services:
              jenkins:
                container_name: jenkins
                image: jenkins/jenkins:lts
                ports:
                  - "8080:8080"
                volumes:
                  - "$PWD/jenkins_home:/var/jenkins_home"
                networks:
                  - net
              remote_host:
                container_name: remote-host
                image: remote-host
                build: 
                  context: centos7
                networks:
                  - net


   -   As the line below **"volumes"** suggests, we copy the data **from container** to **VM directory** (local).

               "$PWD/jenkins_home:/var/jenkins_home"
   
   -  **SAVE** docker-compose yaml file under **/home/centos/jenkins-data** directory

   -  To run docker compose 

            foreground mode:  docker-compose up
            detach mode:      docker-compose up -d
             
   -  Copy public ip of the **docker-compose machine** and paste it to your browser then add port 8080, you should be able to see **Jenkins**
      on your browser.
      
   -  Take the initial pwd from the given directory on Jeknins GUI and create your credentials.
   -  To check jenkins container on your machine:
  
            docket container ls

   -  To check images on your machine:

            docker images
            
   -  If you need to stop jenkins, run:

            docker-compose down
            
   -  After logging into Jenkins Dashboard, install suggested plugins.

<br />

**Let's dockerize centos machine now**

   I am in **jenkins-data** directory. Now, lets create one more directory:
   
            mkdir centos7
            cd centos7
            
<br />            
            
**OUR TASK:** 

Let's imagine, we have 100s of **VMs** for our task. They are too haeavy, and expensive. Additionally, its hard to deploy, migrate as well as hard to switch the environment. 

We decided to **containerize** everything because **containers are more secure than VMs**. There are more layers involved in VMs, so you have to deal with more security. Since containers have less layers, you deal with less security. 

   -  Create a **Dockerfile** under centos7 directory.

            FROM centos:7
            
            RUN yum -y install mysql

            RUN yum -y install python3 \
                  python3-pip  \
                  python3-setuptools \
                && pip3 install --upgrade awscli

            RUN yum -y install openssh-server

            RUN useradd remote_user && \
                echo "remote_user:1234" | passwd remote_user --stdin && \
                mkdir /home/remote_user/.ssh && \
                chmod 700 /home/remote_user/.ssh

   -  We want to write everything in .ssh folder:

            cd .ssh
            (Current dir. /home/centos/.ssh)
            
   -  If we create our **keys**, we'll be in **.ssh folder**.

   -  To create keys run below:

            ssh-keygen
            
            Two files are created:
               id_rsa
               id_rsa.pub
               
   -  Now, lets check folders we have:

            cd jenkins-data
                 centos7
                 docker-compose.yml
                 jenkins-home
                 
            cd centos7
            
   -  Lets change the name of the keys for centos user so we can differentiate.

            ssh-keygen -f remote-key
            
            pwd
               centoss7
               
   -  Since we created ssh keys, we have to **copy** these keys to the container. Therefore, we add another line in Dockerfile.

            COPY remote-key.pub /home/remote_user/.ssh/authorized_keys
            
**Note**: Make sure you copy only **.pub** file **not the private file**. Otherwise, it would create huge security risk.

   -  Give absolute right to user **"remote_user"** to add to Dockerfile

            RUN chown remote_user:remote_user -R /home/remote_user/.ssh && \
            chmod 600 /home/remote_user/.ssh/authorized_keys
            
   -  Add an agent and execute the container.

            RUN /usr/sbin/sshd-keygen
            CMD /usr/sbin/sshd -D

   - Check what is inside **jenkins_data** directory

            ll
              centos7
              docker-compose.yml
              jenkins-home
              
   -  Update owner of centos7 directory

            sudo chown -R 1000:1000 centos7/
            
   -  Run docker-compose file

            docker-compose up -d 
            
   -  We see that we dont have image, so we need to build docker compose

            docker-compose build (Builds the docker image)
            
   -  Run docker-compose file again

            docker-compose up -d
            (Now, we can see remote_host image coming up)
            
   -  Accces into container

            docker exec -it jenkins bash
            (we are inside jenkins)
            
**Note:** We run all jenkins jobs as jenkins user.  


   -  We should be able to **shh from jenkins to remote host**

            ssh remote_user@remote_host
            
   -  Copy files between container and to another container

            docker cp remote-key jenkins:/tmp/remote-key
            
   -  We can go from one container to another container.

            ssh -i /tmp/remote-key remote_user@remote_host
            
            
   -  Go to Jenkins Dashboard and search for ssh plugin.

            Manage Jenkins > Manage Plugins > Available
            
   -  Then go to Jenkins >  Global Credentials > Add Credentials > SSH username and private key

            Copy the key from /tmp/remote-key directory in jenkins machine 
            and paste for private key on Jenkins Dashboard.
            
   -  Go to SSH Sites and add credentials for remote_user.

   -  Check Connection

            Successfull!
        
   -  Run First JOB in container!

            BUILD 
                 Execute shell script on remote host using ssh
            COMMAND
                 NAME=Sharks
                 echo "Hello $Name.Current date and time is $(date)" > /tmp/remote-file
             
   -  SAVE and BUILD NOW Jenkins Job.

             SUCCESS !!!
             
**LET'S DOCKERIZE MYSQL DB NOW**    
   
Lets add more lines for MYSQL DB in **docker-compose.yml** file

               db_host:
                 container_name: db
                 image: mysql:5.7
                 environment:
                   - "MYSQL_ROOT_PASSWORD=1234"
                 volumes:
                   - "$PWD/db_data:/var/lib/mysql"
                 networks:
                   - net
             networks:
               net:  

   -  Make sure you are in correct directory where your docker-compose.yml file exists.

               /home/centos/jenkins_data
                   centos7
                   docker-compose.yml
                   jenkins_home
                   
   -  Run your docker-compose.yml file

               docker-compose up -d
               
**Note:** We do not build image first before run because we pull image from docker hub. 

   -  Now, we are pulling mysql image from docker hub

               Creating DB ...
               
   -  Go inside db container

               docker exec -it db bash 
               
   -  Do go inside database using password

               mysql -u root -p
               
               Enter pwd:
               
               Then, you are inside DB.
               
Now we have **remote_host(VM)** and we have **Jenkins(VM)**.

Everything, we will run on remote_host and will write **data inside DB(VM).**

**Jenkins** will do orchestration for **CI/CD.**

   -  Go inside remote_host container

                docker exec -it remote-host bash
                
Our goal is to write the data into **MySQL DB** and backup the data in **AWS.**

In order to do that we need clients:

   -  AWS CLI
   -  MySQL API

   -  Now, we do run docker-compose file
   
                  docker-compose build
                  docker-compose up -d
                  
                  docker-compose build: Because we are using Dockerfile here, we create our own image and not
                  pulling from Docker Hub.
                  
                  
 Now, we have 2 APIs configured.
 
 Let's go to MySQL DB.
 
 We are going to connect from remote_host to MySQL DB.
 
    -  docker exec -it remote_host bash
    -  mysql -u root -h db_host -p1234
    
So now...

   -  We are connected to DB from remote_host
   -  We are also connected to DB inside DB container

**And, now we can create databases and see data inside those databases.**

<br />

**Further solution...**

Container itself is **ephemeral**. Eventhough we persist the data in VM this is still not ideal. 

Lets say, there are 2 VMs and there is one Elastic Block Storage (**EBS**) on one of 2 VMs. If VM with EBS goes down, you would loose the data.
Therefore, we still need to persist our data.

**Solution....**

We can use S3 bucket, because...

   -  S3 bucket iis faster
   -  Enable Versioning
   -  S3 Bucket Policy
   -  Cross Regional Replication
   -  Has almost 5TB of Storage

**Note:** We'll use S3 Bucket in a different project not here.

    
    
 




               

                

                   

             
             
            


           
 
        
        
            
            
            
            
            
            

          
            
            
            
            
            
            
      

          
            
            
            
            

                         
            












            
            
            
            


            
            
            




            
            
            
            
            
            
            
            
            
            
      
      
            
            
          






























      
     
     
     
     
     



     








     
