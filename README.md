Hello everyone! Before we start, let me first introduce myself.

### Who am I

- My name is Sergey Kibish
- I am Full Stack Web Developer at Transact Pro
- I believe that simplicity is a key to anything
- I love good music. Listen it and if I have time, to play it.

And, let’s start

**Docker in da house**

### Today I will talk about

- A little bit - what is Docker. Main concepts, so we will have correct vision of it
- During my talk I will answer a question: Why I recommend to use it in development
- Also I prepared a demo - list/TODO application to show abilities of Docker (PHP, MySQL, NGINX and something more)
- Q&A time is there will be time left :)

### So, what is docker?

Who knows what is docker - raise your hands!

Good. So, I think many of you heard about this [container] when listened about docker.

Why it is a buzz word and why it is so popular now.

>http://searchservervirtualization.techtarget.com/feature/A-brief-history-of-Docker-Containers-overnight-success

### A little bit of background facts.

Technology upon which Docker’s software was originally built, were introduced in 2008. The idea of containers has been around since the early days of Unix with the chroot command. The problem was, that applications all rely on a common OS kernel, this approach can work only for applications that share the exact OS version. Docker found a way to address this limitation.

### What it gives us
> http://www.docker.com/what-docker

- Lightweight - Containers running on a single machine all share the same operating system kernel so they start instantly and make more efficient use of RAM.
- Open - Docker containers are based on open standards allowing containers to run on all major Linux distributions and Microsoft operating systems with support for every infrastructure
- Secure - Containers isolate applications from each other and the underlying infrastructure while providing an added layer of protection for the application

We will look deeper further.

Another thing, why docker is popular is docker hub. It is an open docker images repository. It is open project, so you can run your own repository too. You can develop image, upload it and someone can use it. It has also official images, like php, reds, mysql etc.

### So, why I encourage you to use it in development?

> https://medium.com/iron-io-blog/why-and-how-to-use-docker-for-development-a156c1de3b24#.1t1u7iv70

- Consistent development environments for your entire team. All developers use the same OS, same system libraries, same language runtime, no matter what host OS they are using (even Windows if you can believe it).
- The development environment is the exactly the same as the production environment. Meaning you can deploy and it will “just work”.
- You only need Docker to develop. You don’t need to install a bunch of language environments on your machine. Want to run a Ruby script but don’t have Ruby installed? Run it in a Ruby Docker image.
- Can use multiple language versions without having to resort to all the hackarounds for your language. Want php version 7, 3 or 4? Just download container with language of needed version.
- Can still use your favorite editor/IDE as you normally do. No need for running a VM in VirtualBox and SSHing in and developing from the shell just so you can build/run on a Linux box.

### And it's easy to use

I will show you now.

If you know git, some commands will look familiar.

Let's install ubutnu and centos.

But before that - about installation. It also easy to do that.

If you a are using Mac or Windows you can install Docker toolbox.

After that you will be ready to go.

What I was doing? Aha! Installing ubuntu and centos

[switch to console]

`$ docker pull ubuntu`

`$ docker pull centos`

To run image you can type:
`$ docker run ubuntu bash`

But I will run it automatically with additional flag like this:

`$ docker run --rm -it ubuntu bash` - Get inside ubuntu

Ok, we are inside of ubuntu. Let's check that we are really in ubuntu. Type:

`$ cat /etc/*-release`

Good. I will exit it, and let's do the same for centos:

`$ docker run --rm -it centos bash` - Get inside centos

`$ cat /etc/*-release`

Cool!

Remember I have told that Docker shares the kernel? Let's check kernel now

`$ uname -r`

As you can see kerenl is of virtual machine boot2docker.

Ok. basically we are ready to setup our demo app.

It will be easy. We will up and ready in less than 20 minutes. Trust me.

But before that a question. We are lazy, are we?

### Let me introduce you to Docker compose.

What is it? From official documentation on github:

> “Compose is a tool for defining and running multi-container applications with Docker. With Compose, you define a multi-container application in a single file, then spin your application up in a single command which does everything that needs to be done to get it running.” [drmaatic pause]

### Not bad, hey?

### Let’s write docker compose file for our project

It is a yml file.

I will start from nginx server - I will name it web.

Here it is, a description of the web container. What we see here.
We will create container on top of official nginx latest image.

We are binding container nginx port to our host 80 port, so we will be able to connect through port 80.

We are giving access to our project (dot) and mapping it inside of container + we are mapping our configuration file in container to overwrite defaults.

Next massive step is to connect somehow web with php. Because nginx must know about php-fpm. Who to do this?
So, we are writing following command. We are linking web container to php container. This way they will know about each other. How? With some magic? No. Through hosts file.

Let’s write configuration for php container.

We have written for it separate docker file file (because we use Lumen and it has some additional depenedencies), so we need to build image. Compose then will create container from it (we will talk about Dockerfiles later).

This is how it will be done. With build command.

Image will be build from dockerfile which is in this directory. This image will be used to build containers further for current project.

And again, we are sharing our project, so php-fpm will know about php files.

I’m passing here optional php-fpm config file to overwrite some defaults.

What we have left? Database. Who must know about database? PHP! Let’s link it to the database!

Database.

We are getting latest mysql image.

Binding our HOST port to container exposed default port.

And setting mandatory environment variable which defines password for root user of database and database name.

And we finished it! Let’s see if it works?

`$ cd project `

`$ docker-compose up -d`

Let's go to the browser.

No...

### Why?

We definitely forgot about something.

We need composer to install and bootstrap all needed dependencies.

### Composer

Composer will be installed as separate container and in the following way.

`$ docker pull composer/composer`

There is an composer image already on docker hub. So we will pull it down.

Let’s go back and install needed dependencies.

`$ docker run --rm -v $(pwd):/app composer/composer install`

As a hint you can save it as alias `compose` and use it as normal composer like `composer install`

Ok, we have installed dependencies.

Now we need to migrate database.

How to do this easily?

Let’s run artisan migrate command inside php container.

`$ docker exec php-fpm php aritsan migrate`

We migrated a databse! Cool! Let see if it works.

Yes, it works!

###

To show you, that you can edit files like normal developer in ide or editor, let's go to the directory with todo title and change it.

### Dockerfile

I have mentioned dockerfiles. What is it? Basically it is a set of instructions that must be run by Docker engine to build your image.

Let's look at our php dockerfile from which image was build.

Most dockerfiles will look he same.

First command FROM is mandatory. It tells Docker what is the base image for your container.

We already have seen this command in docker-compose config file.

Second command is here RUN. Looks like bash command here with apt-get to install all needed stuff.

But really what it does is - it will execute this command that we have written in a new layer on top of the current image and will commit the results. The resulting committed image will be used for next step in the docker file.

Your image is like a cake. Remember it like this one. This means, that when you will add something to your image and write additional RUN command - image will not be build all over again. It will understand what was changed and will build from this point.

EXPOSE command tells docker what default port will be shown to the outer world.

CMD (command) is telling - when container will start - what command to execute. In this case we are executing php-fpm. It has two ways of writing - like string or array, if like array it will executed as regular command in shell. If as string, it will be executed like sh -c.

So, remember about cakes.

### Imagine, that our app evolved

We not only have basic stuff, but also have now queue - where our tasks will go now, worker who will disassemble it and we have generator, because, we do not want to think about tasks. They will be generated automatically.

We see how fast they start and look (htop) - containers are using nothing of resources. This is cool. This is example of their lightweight. Remember that they ALL are running some operating systems.

Also you can see logs of all of your containers. They are pretty. Let's look on them.

### OK, after all of that, there is a last question. Do we use it?

Yes!

We are developing our apps in microservices architecture.

Now we have **more than** 20 microservices.

And all of them are spinning on all team member machines.

And this is not counting connections to MySQL, redis, queues.

### That's all folks!

It's a question time

### Fun dockerizing!
