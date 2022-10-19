## Containers 1: Deploy a Laravel App into a Docker Container

### Get Started

Whenever you search on google you find tutorials on how to deploy Laravel App with docker-compose not just docker container. 
#### But what is the difference between Docker and Docker Compose? 
1. Docker run is entirely command line based, while docker-compose reads configuration data from a YAML file. 
2. Docker run can only start one container at a time, while docker-compose will configure and run multiple.

When you are working with Laravel, you need multiple services to be configured while starting Laravel project, like: MySQL database, redis, mailhog and other services. All these can be configured together using docker-compose and they become connected to each other. 
But sometimes we just need to configure one container for our laravel project without reconfiguring all other services. 

#### Here this tutorial come in handy, I will explain step by step how to install Laravel App project into a Docker container. 

### Prerequisite

Laravel project downloaded on your local environment.

#### Step 1: Create a DockerFile

Let's start of by adding a docker file to the laravel project. 
In the root of the project add a new file called Dockerfile.

#### Step 2: Choose the right base image

After adding this Dockerfile, we need to write the first instruction which is FROM.
FROM instruction is used to specify the base image. In our case the image will be operating system (alpine preferable) plus a run time environment which is PHP version that our project needs.
Inorder to know which image to use we need to 
1. Navigate to https://hub.docker.com/.
2. Write PHP in the search field on the top.
3. Click on PHP (Docker Official Image) - you will be navigated to php page
4. Click on tags
5. Write php version of the project in Filter Tags search input.
6. Copy the tag of this image
7. Paste the tag in the Dockerfile as follow: FROM php:7.2.34-zts-alpine3.12

#### Step 3: Changing the working directory

For php projects we need to move all our files to /var/www/html folder. So let us add below FROM instruction:
WORKDIR /var/www/html

#### Step 3: Execluding files and directories

Before copying laravel app directories and files we need to execlude couple of folders that are not needed to be transfered to the image.
We will add folder in the directory and name it: .dockerignore
Inside it we will add the following:
.env
vendor/

So when building an image, docker will not copy a large directory to docker engine.
This will reduce build context massively and unneeded.

#### Step 4: Copying files and Directories

Now that we have a base image, and we are in the correct directory, next step is to copy the application files into the image. 
To do so, we need to add this instruction: COPY . .

#### Step 5: Running commands

Next step we are going to install project dependencies using composer. We will use the RUN command.
Add this line: RUN composer install

#### Step 6: Setting environment variables

We need to set environment variables of the project, it will include your database connection and all other variables you need. 
And add the following command to set your variables:
ENV DB_HOST=localhost
...
And change in your .env file DB_HOST=localhost to DB_HOST=${DB_HOST}

#### Step 7: Exposing ports

We need to set environment variables of the project, it will include your database connection and all other variables you need. 
And add the following command to set your variables:
ENV DB_HOST=localhost
...
And change in your .env file DB_HOST=localhost to DB_HOST=${DB_HOST}


#### Conclusion
