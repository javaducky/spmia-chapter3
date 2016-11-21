#Introduction
All of the code examples in this book will include maven files for building docker images of the services being built.  As we progress through the book we will be leveraging more and more infrastructure as we build our services out.   These additional pieces of infrastructure will also be docker containers.  

#Software Needed
All of the codes instances have been built and compiled on a Mac running OS X.  We leverage Kitematic to build docker images.  Kitematics will install docker, docker-machine and docker-compose.  Docker is the core-runtime for docker containers.  Docker-machine providers a virtual-machine instance that the docker containers will run, while docker-compose providers orchestration capabilities for  starting and stopping groups of docker-machines.  Download [Kitematic](https://kitematic.com/) and follow the instructions for installing the software.

We are going to build and start all of our machines from the command-line.  So once you start kitematic you should can open a command line window by pressing the command-line CLI button on left hand of the screen.

#Building the Docker Images for Chapter 3
To build the code examples for Chapter 3 as a docker image, use the command-line window opened with Kitematic and then change to the directory where you have downloaded the chapter 3 source code.

Run the following maven commands in the following directories:
    - confsvr  
    - licensing-service

This command will execute the [Spotify docker plugin](https://github.com/spotify/docker-maven-plugin) defined in the pom.xml file.  

   mvn clean package docker:build

This will build the docker image for the licensing service in Chapter 3.  Note:  The docker cli does not see all of the path variables you might have in your normal path, so you might have to set your path to point to your maven bin directory.

If everything builds successfully for each directory you should see a message indicating that the build was successful for that project/

#Running the Application for Chapter 3

Now we are going to use docker-compose to start the actual image.  Since our first attempt at the licensing service is completely
self contained and does not even talk to a database, the code only one docker image to start.  To start the docker image,
change to the docker directory (using the command window opened from Kitematic) in your chapter 3 source code.  Issue the following docker-compose command:

   docker-compose -f common/docker-compose.yml up

This command will start up a Spring Cloud Configuration Instance, a licensing-service instance and a postgres-database.  Note: the postgres database will be populated with data contained in the licensing-service/src/main/resources/schema.sql file.

If everything starts correctly you should see standard output from all of the different services fly by. At this point the service is running in docker container in a Virtual VM (e.g. virtual box).

#Testing Chapter 3 Code

To hit the endpoint we need to know the virtual machine IP that was assigned to the server by Kitematic.  To find this out the IP address of the machine you can issue the following command to docker-machine (again using a command window started by Kitematic).

docker-machine ip

This will return the IP of the machine in question.

At this point you can use post man or curl to hit the endpoint in question.  For instance if I were to issue the following curl command to the licensing service, I should get data back:

http://192.168.99.100:8080/v1/organizations/e254f8c-c442-4ebe-a82a-e2fc1d1ff78a/licenses/f3831f8c-c338-4ebe-a82a-e2fc1d1ff78a

You should get back the following JSON data.

{
    "licenseId": "f3831f8c-c338-4ebe-a82a-e2fc1d1ff78a",
    "organizationId": "e254f8c-c442-4ebe-a82a-e2fc1d1ff78a",
    "productName": "customer-crm-co",
    "licenseType": "user",
    "licenseMax": 100,
    "licenseAllocated": 5,
    "comment": "I AM IN THE DEFAULT"
}
