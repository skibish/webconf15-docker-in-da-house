Hello everyone! Let me first introduce myself. I am ...

And, let’s start

**Docker in da house**

### About what I will talk today:

- A little bit about what is docker. Main concepts, so we will have correct vision of it
- Why I recommend to use it in development
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

Another thing, why docker is popular is docker hub. It is an open docker images repository. It is open project, so you can run your own repository too. So, you can develop image, upload it and someone can use it. It has also official images, like php, reds, mysql etc.

### So, why I encourage you to use it in development?

> https://medium.com/iron-io-blog/why-and-how-to-use-docker-for-development-a156c1de3b24#.1t1u7iv70

- Consistent development environments for your entire team. All developers use the same OS, same system libraries, same language runtime, no matter what host OS they are using (even Windows if you can believe it).
- The development environment is the exact same as the production environment. Meaning you can deploy and it will “just work”.
- You only need Docker to develop. You don’t need to install a bunch of language environments on your machine. Want to run a Ruby script but don’t have Ruby installed? Run it in a Ruby Docker image.
- Can use multiple language versions without having to resort to all the hackarounds for your language. Want php version 7, 3 or 4? Just download container with language of needed version.
- Deployment is easy. If it runs in your container, it will run on your server just the same. Just package up your code and deploy it on a server with the same image or push a new Docker image with your code in it and run that new image.
- Can still use your favorite editor/IDE as you normally do. No need for running a VM in VirtualBox and SSHing in and developing from the shell just so you can build/run on a Linux box.

### And it's easy to use

I will show you know

If you know git, some commands will look familiar.

Let's install ubutnu and centos.

`$ docker pull ubuntu`

`$ docker pull centos`

Let's run it interactively:

`$ docker run --rm -it ubuntu bash` - Get inside ubuntu

`yum`

`$ docker run --rm -it centos bash` - Get inside centos

`apt-get`

`$ cat /etc/*-release` - info about OS

`$ uname -r` - will show, that kernel is the same


So let’s setup our demo app.

It will be easy. We will up and ready in less than 20 minutes. Trust me.

But before that a question. We are lazy, are we?

### Let me introduce you to Docker compose.

What it is? From official documentation on github:

> “Compose is a tool for defining and running multi-container applications with Docker. With Compose, you define a multi-container application in a single file, then spin your application up in a single command which does everything that needs to be done to get it running.” [drmaatic pause]

### Not bad, hey?

### Let’s write docker compose file

It is a yml file.

I will start from nginx server - I will name it web.

Here it is, a description of the web. What we see here.
We will create container on top of official nginx latest image.

We are binding container nginx port to our host 80 port, so we will be able to connect through port 80.

We are giving access to container to our project (dot) and mapping it inside of container + we are mapping our configuration file in container to overwrite defaults.

Next massive step is to connect somehow web with php.
So, we are writing following command. We are linking web container to php container. This way they will know about each other. How? Through hosts file.

Let’s write configuration for php container.

We have written for it separate docker file file (because we use Lumen and it has some additional depenedencies), so we need to build image. Compose then will create container from it (we will talk about Dockerfiles later).

This is how it will be done. With build command.

Image will be build from dockerfile which is in this directory and tagged and this image will be used to build containers further for current project.

And again, we are sharing our project, so php-fpm will know about php files.

I’m passing here optional php-fpm config file to overwrite some defaults.

What we have left? Database. Who must know about database? PHP! Let’s link it to database!

Database.

We are getting latest mysql image.

Binding our HOST port to container exposed default port.

And setting mandatory environment variable which defines password for root user of database and database name.

And we finished it! Let’s see if it works?

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

As a hint you can save it as alias compose and use it as normal composer like `composer install`

Ok, we have installed dependencies.

Now we need to migrate database.

How to do this easily?

Let’s run artisan migrate command inside php container.

`$ docker exec php-fpm php aritsan migrate`

We migrated a databse! Cool! Let see if it works.


............ (about docker images etc.)

### That's all folks!

It's a question time

### Fun dockerizing!
