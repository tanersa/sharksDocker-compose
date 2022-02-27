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
                  

   
            
            
            
            
            
            
            
      
      
            
            
          






























      
     
     
     
     
     



     








     
