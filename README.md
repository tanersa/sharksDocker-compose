# Docker - DockerCompose


![alt text](https://github.com/tanersa/sharksJenkins/blob/master/Docker.png)

<br />

 **Docker** is a container runtime that is **not SERVERLESS**. Therefore, Docker requires to be managed by developers during building and running applications. 
 In reality, there are still servers being used during leveraging Docker but they are abstracted away from app development. 
 Example of some serverless services are **Kubernetes, AWS Lambda**, and **MS Azure functions**. 
 
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

Let's say, we would like to create **Tomcat** container



     








     
