<!--
    Post{
        subtitle: "Learn how to use Docker and upload its images to Docker Hub"
        image: "posts/web/docker.png",
        image_decription: "Image from the official website",
        tags: "How use Docker code",
    }
-->

<!-- Link -->

[Steadylearner]: https://www.steadylearner.com
[Docker Website]: https://docs.docker.com/get-started
[How to install Docker]: https://www.google.com/search?q=how+to+install+docker
[How to deploy a container with Docker]: https://thenewstack.io/how-to-deploy-a-container-with-docker/
[Docker Curriculum]: https://docker-curriculum.com/
[Docker Hub]: https://hub.docker.com/
[Docker lifecycle]: (https://medium.com/@BeNitinAgarwal/lifecycle-of-docker-container-d2da9f85959).
[AWS]: https://aws.amazon.com
[Elastic Beanstalk]: https://aws.amazon.com/pt/elasticbeanstalk/
[ECS]: https://aws.amazon.com/ecs/
[Cloud​Formation]: https://aws.amazon.com/pt/cloudformation/
[Yarn]: https://yarnpkg.com/lang/en/
[Express]: https://expressjs.com/

[comment]: # (Atualizei o link Docker lifecycle, como sugerido no relatório)

<!-- / -->

<!-- Steadylearner Post -->

[Steadylearner Blog]: https://www.steadylearner.com/blog

<!-- / -->

<!-- Steadylearner Twitter and LinkedIn  -->

[Twitter]: https://twitter.com/steadylearner_p
[LinkedIn]: https://www.linkedin.com/in/steady-learner-3151b7164/

<!--  -->

In this post, we'll learn how to use Docker commands. We will make a web app inside a Docker container and turn it into a Docker image. We'll also learn how to upload it to [Docker Hub].

<br />

<h2 class="red-white">[Prerequisite]</h2>

1. [How to install Docker]
2. [Docker Website], [Docker Curriculum]
3. [How to deploy a container with Docker], [Docker lifecycle]

---

First, you must install Docker, if you don't have it yet. Type **$ docker** on your machine and you will be shown how to proceed with the installation. Or just search how to install it on your browser.

This post is a summary of [Docker Website], [Docker Curriculum] etc. I hope you read them first, but you don't have to spend a lot of time with them. We'll learn how to deploy a web app and microservices to [AWS] with other [Steadylearner Blog] posts.

<br />

<h2 class="blue">Table of Contents</h2>

1. Confirm installation with Nginx
2. Set up your development environment with Docker
3. How to move your local files and folders to docker containers
4. How to use the web framework with docker containers
5. How to modify the network ports of docker images
6. Docker Images and Containers
7. How to upload your Docker images to Docker Hub
8. Conclusion

---

<br />

## 1. Confirm installation with Nginx

I hope you can install Docker. Before we learn how each Docker command works, let's test whether or not these can show some results on your machine.

Use them in your CLI.

```console
$docker search nginx
$docker pull nginx
$docker run --name nginx-webserver -p 80:80 nginx
```

Then, access [localhost](http://localhost.com). This will be shown in your browser:

[comment]: # (O link anterior http://localhost não funcionava. Mostrei no relatório que enviei por e-mail. Atualizei para http://locallhost.com/. Se o anterior for intencional, por favor me avise)

```console
Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

Realize that you only need a few commands to use Docker. You could also start a Docker container with a specific name and execute bash comamnds with these.

```console
$docker run --name nginx-webserver -p 80:80 -d nginx
$docker exec -it CONTAINER_ID bash
```

<br />

## 2. Set up your development environment with Docker

In this section, we'll learn how to set up a default Docker image with Ubuntu. It will be possible to reuse this image later. If you use another OS, please, use that instead and refer to this part.

Start with **pull** commands to download the official ubuntu image from [Docker Hub]. If Docker Hub is something new for you, you can compare it to GitHub because of its repositories.

```console
$docker pull ubuntu
```

Now, make a container in your machine. To download minimal softwares, use sh or bash commands in it with this:

```console
$docker run -it ubuntu sh
```

Start by installing **CURL** to download other programs.

```console
$apt-get update
$apt-get install curl
$curl https://www.steadylearner.com
```

If you are out of the container, restart it with this:

```console
$docker exec -it CONTAINER_ID bash
```

You can find the CONTAINER_ID with **docker ps -a**. This is the command you will use often and it will show you some useful metadata of Docker containers.

We'll make a simple Node "Hello, World" web app example in this post. Let's start by configuring the Node development environment. Follow these steps if you want to use the same project as this post.

Also, you can use [$docker run -d steadylearner/ubuntu_node](https://cloud.docker.com/u/steadylearner/repository/docker/steadylearner/ubuntu_node) instead.  

You should be inside your docker container to use them.

<details>
  <summary class="red-white font-normal hover cursor-pointer transition-half">Node, NPM, Yarn</summary>

```console
curl -sL https://deb.nodesource.com/setup_12.x | bash
```

This will be displayed:

```console
## Run `sudo apt-get install -y nodejs` to install Node.js 12.x and npm
## You may also need development tools to build native addons:
    sudo apt-get install gcc g++ make
## To install the Yarn package manager, run:
    curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
    sudo apt-get update && sudo apt-get install yarn
```

You should use commands without sudo.

```console
apt-get install gcc g++ make
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

apt-get update && apt-get install yarn
```

Follow those commmands and install them all.

Test Node work with this:

```console
$node
$console.log("Hello from www.steadylearner.com");
```

</details>

<details>
  <summary class="red-white font-normal hover cursor-pointer transition-half">Vim</summary>

Use this command with --assume-yes or -y to skip relevant install questions.

```console
apt install --assume-yes vim
```

Vim text editor will be installed. Now, use this command to use it:

```console
$vim hello.js
```

Edit your hello.js file with this and **:wq** to save and quit from the Vim.

```js
// hello.js
console.log("Hello from www.steadylearner.com");
```

Use this command to verify that Node is installed correctly:

```console
$node hello.js
// Hello from www.steadylearner.com
```

</details>

<details>
  <summary class="red-white font-normal hover cursor-pointer transition-half">Git</summary>

```console
$apt-get --assume-yes git-core
```

This will install Git and verify that it is installed and its version:

```console
$git --version
```

Then, get your GitHub username and email from your local machine:

```console
$git config --get user.name
$git config --get user.email
```

Use them in the Docker container to operate Git inside:

```console
$git config --global user.name yourname
$git config --global user.name youremail
```

Make use of the same command (--get) before checking them in your Docker container.

Test Git clone work to download files from your previous GitHub repositories. For example, clone [steadylearner/docker-examples](http://github.com/steadylearner/docker-examples) repository with this:

[comment]: # (Apenas adicionei um link para o repositório)

```console
$git clone https://github.com/steadylearner/docker-examples.git
```

</details>

I hope you can install everything you think is necessary in your Docker container.

You can skip this Yarn relevant part and use default npm commands instead. Otherwise, read [this post](https://linuxize.com/post/how-to-install-yarn-on-ubuntu-18-04/) for more information.

Verify the [Yarn] version first in it.

```console
$yarn -v
```

It will show the version of your Yarn.

Next, use these commands to use Node project:

```console
$cd /home
$mkdir node && cd node
$yarn init
$yarn add chalk
```

Test NPM or Yarn work with NPM modules with these:

```js
// Start with $node in your console and use each command.

const chalk = require("chalk");
const blue = chalk.blue;
const hello = blue("Hello from www.steadylearner.com");
console.log(hello);
```

It should have shown  **Hello from www.steadylearner.com** message in your console.

We verified that NPM packages work in your docker container with this.

[comment]: # (Há algo faltando aqui?)

If you want, make alias for this directory also similar to this.

```console
$vim ~/.bashrc
```

Type this and :wq to save and quit.

```bash
alias work="cd /home/node"
```

Use **$source ~/.bashrc** and you can use your node project with **$work** whenever you want. Also, you can define WORKDIR later with **Dockerfile** or **docker-compsose.yml** for the same purpose.

There will be many Docker containers in your machine. Use these commands to remove unnecessary ones:

**1.** List and remove previous Docker containers.

```console
$docker ps -a
```

The list of instances that you ran before will be shown.

**2.** Remove what you don't need.

```console
$docker stop containerid
$docker rm containerid
```

or

```console
$docker rm containerid -f
```

<br />

## 3. How to move your local files and folders to docker containers

We can use Git commands to download files from the GitHub into your containers. Moreover, you can use Docker commands to move local files and folders to your Docker containers and vice versa.

Refer to these examples or **docker cp --help**.

**1.** Files

```console
$docker cp from_localhost.txt containerid:/from_localhost.txt
$docker cp containerid:/from_docker from_docker.txt
```

**2.** Folders

```console
$docker cp from_localhost containerid:/from_localhost
$docker cp containerid:/from_localhost from_localhost
```

<br />

## 4. How to use web frameworks with docker containers

We installed Node relevant softwares for this part. If you use web frameworks from other languages, please, refer only to the workflow of this section.

<details>
  <summary class="red-white font-normal hover cursor-pointer transition-half">Express</summary>

Install the dependencies we'll use inside the docker container with this:

```console
$yarn add express chalk
```

Next, we'll build "Hello, World!" app with the JavaScript code below.

```js
// server.js
const express = require('express')
const chalk = require("chalk");

const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello, World!'))

const blue = chalk.blue
const target = blue(`http://localhost:${port}`)

app.listen(port, () => console.log(`Express Server ready at ${target}`))
```

Then, **$node server.js** will display this message:

```console
[Express] Server ready at http://localhost:3000
```

But **$curl http://localhost:3000** or visiting it in your browser won't work yet.

Each container has its own IP to network. We should inspect the docker container with **$docker inspect CONTAINER_ID > inspect.txt** to extract the information from it.

You can find its local IP at the end of inspect.txt and it will be similar to **172.17.0.2**. Save your time doing getIP.js and **$node getIP.js**.

```js
const fs = require('fs')

const filename = "inspect.txt";

fs.readFile(filename, 'utf8', function(err, data) {
  if (err) throw err;

  // console.log(`Read ${filename}`);
  const dataObject = JSON.parse(data);

  // console.log(payload);
  // console.log(typeof payload);

  const ip = dataObject[0].NetworkSettings.IPAddress;
  console.log(`IP is ${ip}`);
});
```

You can use the [docker inspect command](https://docs.docker.com/engine/reference/commandline/inspect/) as well.

Test the IP with $curl http://172.17.0.2:3000/ or verify it with your browser.

If you can see this message, realize that you can actually develop web apps on your local machine with Docker.

```console
Hello, World!
```

</details>

<br />

## 5. How to modify the network ports of docker images

In the previous part, we had to find the network port for the web framework to visit it. Alternatively, [you can start with your custom port](https://www.google.com/search?&q=how+to+assign+port+for+docker+container).

```console
$docker run -it --name ubuntu_node -p 80:80 ubuntu
```

You can also use it with **-d** to [make the container run in background.](https://docs.docker.com/engine/reference/run/).

```console
docker run -d --name ubuntu_node -p 80:80 ubuntu:latest
```

Refer to this to find out what happens here better.

'By default, the port on the host (container) is mapped to 0.0.0.0, which means all IP addresses. You can specify a particular IP when defining the port mapping, for example, -p 127.0.0.1:80:80'

<br />

## 6. Docker Images and Containers

You may find yourself confused by the difference between Docker container and image. Just think of the images as classes and containers as instances that you are using on your machine. You can:

1. Pull or run (pull and start) images and it will make docker containers from them on your local machine.

2. Edit files in your containers with **$docker exec -it containername bash**.

3. Make images from the containers with **$docker commit containername YourDockerHub/image && docker push account/image**.

Feel free to start with Dockerfile instead of **1.** and **2.** and commit your docker images as well. We'll learn that in another [Steadylearner Blog] with [Elastic Beanstalk].

<br />

## 7. How to upload your Docker images to Docker Hub

Now, we are going to learn how to [create a repository first at Docker Hub](https://cloud.docker.com/repository/create) with the example we made.

First, login with this command:

```console
$docker login
```

Then, use [$docker commit](https://www.scalyr.com/blog/create-docker-image).

```console
$docker commit ubuntu_node
```

Next, verify the image made from the ubuntu_node container using this:

```console
$docker images
```

Give it a tag (name).

```console
$docker tag imageid steadylearner/ubuntu_node
```

You can execute this command instead:

```console
$docker commit ubuntu_node steadylearner/ubuntu_node
```

Now, you can push your docker image to Docker Hub with this:

```console
$docker push steadylearner/ubuntu_node // yourusername/image
```

Wait for uploading process to complete and use this:

```console
$docker run -it steadylearner/ubuntu_node bash
```

If you want to edit it, just follow the same steps we used before.

Restart the containers with this, if they stop:

```console
$docker restart containerid
$docker exec -it containerid bash
```

To remove container made from steadylearner/ubuntu_node image or yours, you can use this:

```console
$docker stop ubuntu_node
$docker container rm ubuntu_node
$docker image rm ubuntu
```

If you want to rename the container, use this:

```console
$docker container rename randomname ubuntu_node
```

Use yours instead of ubuntu_node or steadylearner/ubuntu_node.

If you modify the project, use the commands similar to this:

```console
$docker commit ubuntu_node steadylearner/ubuntu_node
```

Or with a commit message.

```console
$docker commit --message "Test message and will be similar to github -m option" ubuntu_node steadylearner/ubuntu_node
```

Now, let's push the image made from it to Docker Hub:

```console
$docker push steadylearner/ubuntu_node
```

and use this:

```console
$docker run -it steadylearner/ubuntu_node bash
```

or this, to verify the result:

```console
$docker history steadylearner/ubuntu_node
```

<br />

## 8. Conclusion

I hope you made it all work.  We learned how to install Docker and make it work with Nginx, how to make a Docker container and image and upload them into [Docker Hub].

There are many things to learn. But everything will be easier with examples. In the later posts on the [Steadylearner Blog], we will learn how to deploy the web app with [Elastic Beanstalk] from [AWS] and Dockerfile. We will also learn how to deploy micro services with [ECS], [Cloud​Formation], **docker-compose.yml** etc. So, keep an eye out for updates!

Stay on top of the latest Steadylearner content: follow me on [Twitter].

Do you need **a Full Stack Rust Developer** who can deploy the projects with Docker, AWS etc? Contact me with [LinkedIn] and I will help you.

**Thank you! Share this post with others and help us to grow and improve.**

Reviewed by [Mariana Santos](https://www.linkedin.com/in/mariana-santos-89234a189/?locale=en_US)

[comment]: # (Tudo bem me identificar aqui certo? :)

Refer to these commands if you want more. Use the ID or the name of the containers for them.

### Logs of the container

```console
$docker logs containerid | name
```

### History of the image

```console
#docker history steadylearner/ubuntu_node
```

### Remove unused images

```console
$docker images
$docker image rm dockerimagename or docker rmi
```

### Rename the container

```console
$docker rename randomname whatyouwant
```

### Pause and unpause the containers

```console
$docker pause containerid | name
$docker ps -a
$docker unpuase containerid | name
$docker ps -a
```

### Start and stop them

```console
$docker stop containerid | name
$docker ps -a
$docker start containerid | name
$docker ps -a
```

### Remove containers

```console
$docker container rm containerid | name
```
