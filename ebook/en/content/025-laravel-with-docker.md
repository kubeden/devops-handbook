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

### Creating and writing into Dockerfile

#### Step 1: Create a Dockerfile

Let's start of by adding a docker file to the laravel project. 
So, in the root of the project add a new file called Dockerfile.

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
7. Paste the tag in the Dockerfile as follow: 
```
FROM php:8.0.24-zts-alpine3.16
```

#### Step 3: Changing the working directory

For php projects we need to move all our files to /var/www/html folder. So let us add below FROM instruction:
```
WORKDIR /var/www/html
```

#### Step 3: Execluding files and directories

Before copying laravel app directories and files we need to execlude couple of folders that are not needed to be transfered to the image.
We will add folder in the directory and name it: .dockerignore
Inside it we will add the following:
vendor/

So when building an image, docker will not copy a large directory to docker engine.
This will reduce build context massively and unneeded.

#### Step 4: Installing all prerequisite packages

Before executing composer install, we have to install all the packages needed in our project. 
I will list here the common prerequests for all laravel projects, and you can add to them as much as needed.
```
RUN apk update 
RUN curl -sS https://getcomposer.org/installer | php -- --version=1.10.26 --install-dir=/usr/local/bin --filename=composer
```

#### Step 5: Copying files and directories

Now that we have a base image, and we are in the correct directory, next step is to copy the application files into the image. 
To do so, we need to add this instruction: 
```
COPY . .
```

#### Step 6: Running commands

Next step we are going to install project dependencies using composer. We will use the RUN command.
Add this line: 
```
RUN composer install
```

#### Step 7: Defining entrypoints

Whenever we want to start a laravel application we have to execute this command: php artisan serve --host=0.0.0.0
Same in docker, to start the project we need this command in execute form to start our project, so we will add this line in the last line of Dockerfile

```
CMD ["php","artisan","serve","--host=0.0.0.0"]
```

##### Our final Dockerfile is as follow

```
FROM php:8.0.24-zts-alpine3.16
WORKDIR /var/www/html

RUN apk update 
RUN curl -sS https://getcomposer.org/installer | php -- --version=2.4.3 --install-dir=/usr/local/bin --filename=composer

COPY . .
RUN composer install

CMD ["php","artisan","serve","--host=0.0.0.0"]
```

### Build and run your container

#### Build

Whenever we want to start laravel project, you need to navigate to project files and build it using this command:
```
docker build -t laravel-app .
```

#### Run

When done build you will have to run laravel project using the following command:
```
docker run -d -p 81:8000 --name backend laravel-app
```

#### Open browser

Now in your browser write: localhost:81


### Conclusion
With only couple of commands you can deploye your laravel project on docker, and leave all other configurations as is.


Happy Coding
