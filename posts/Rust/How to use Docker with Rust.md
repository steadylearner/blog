<!--
    Post{
        subtitle: "Learn how to dockerize Rust web projects."
        image: "post/Rust/docker-rust.png",
        image_decription: "Image by Steadylearner",
        tags: "How Docker Rust Actix",
    }
-->

<!-- Link -->

[comment]: # (Os seguintes links não estão funcionando Erro "página não encontrada [React Rust]. Eu exclui links que não estavam sendo utilizados.)

[Steadylearner]: https://www.steadylearner.com
[Rust Full Stack]: https://github.com/steadylearner/Rust-Full-Stack
[React Rust]: https://github.com/steadylearner/Rust-Full-Stack/tree/master/React_Rust

<!-- / -->

<!-- Steadylearner Post -->

[Rust blog posts]: https://www.steadylearner.com/blog/search/Rust
[How to use Docker commands]: https://www.steadylearner.com/blog/read/How-to-use-Docker-commands
[How to use React with Rust Actix]: https://www.steadylearner.com/blog/read/How-to-use-React-with-Rust-Actix

<!-- / -->

<!-- Steadylearner Twitter and LinkedIn  -->

[Twitter]: https://twitter.com/steadylearner_p
[LinkedIn]: https://www.linkedin.com/in/steady-learner-3151b7164/

<!--  -->

In this post, we'll learn how to use [Docker][How to use Docker commands] with Rust web frameworks.

Reviewed by [Mariana Santos](https://www.linkedin.com/in/mariana-santos-89234a189/?locale=en_US)

We already have React and Rust server side code ready at [React Rust]. Clone the repository or use the Docker to see the result first.

```console
$docker run -d -p 80:8000 steadylearner/rust && curl localhost
```

Then, the result will be similar to this:

[![user-signup](https://www.steadylearner.com/static/images//post/React/user-signup.png)][React Rust]

<br />

<h2 class="red-white">[Prerequisite]</h2>

1. Docker
2. Rust

---

I assume you're already familiar with Docker and Rust. If you haven't yet used Docker commands, please, read this Steadylearner post [How to use Docker commands] for more information.

<br />

<h2 class="blue">Table of Contents</h2>

1. Target **0.0.0.0:** to use your project with Docker
2. Prepare files to use
3. Make a Docker image with Dockerfile
4. Verify the result in your machine
5. Conclusion

---

<br />

## 1. Target 0.0.0.0: to use your project with Docker

Perhaps, this is the simplest step, but also the most important to save your time: verify your server side code. In server/actix/src/main.rs of [React Rust], you can see the code similar to this:

```rust
extern crate actix_web;
extern crate actix_files;
extern crate console;

use actix_files as fs;
use actix_web::{App, HttpServer, web, middleware, Result, Responder};

use std::path::PathBuf;

use console::Style;

fn single_page_app() -> Result<fs::NamedFile> {
    let path: PathBuf = PathBuf::from("./public/index.html");
    Ok(fs::NamedFile::open(path)?)
}

pub fn main() {
    std::env::set_var("RUST_LOG", "actix_web=info");
    env_logger::init();

    let blue = Style::new()
        .blue();

    let prefix = "0.0.0.0:";
    let port = 8000; // We will use 80 for aws with env variable.
    let target = format!("{}{}", prefix, port);

    println!("\nServer ready at {}", blue.apply_to(format!("http://{}",&target)));

    HttpServer::new(|| {
        App::new()
            .wrap(middleware::Logger::default())
            .route("/", web::get().to(single_page_app))
            .route("/user", web::get().to(single_page_app))
            .service(fs::Files::new("/", "./public").index_file("index.html"))
    })
    .bind(&target) // Separate prefix, port, target, println! not to show "Not registered service error"
    .unwrap()
    .run()
    .unwrap();
}
```

**Never ever** use **localhost** or **127.0.0.1** etc. **Only** use ports that start with **0.0.0.0:** if you want to place your project in a Docker container later. This is also true for other web frameworks.

The port **8000** is used here to synchronize with React frontend made with Webpack. If you want to deploy it on AWS, use 80 instead. We will learn how to do that in later [Rust blog posts].

If you want more information about the code used here, please read this Steadylearner post [How to use React with Rust Actix].

<br />

## 2. Prepare files to use

Rust is a compiled language. When you use **cargo run --release**, it creates an executable binary file at **target/release/**.

This file will have the same name as your project at **Cargo.toml** and will be **react_actix**.

```toml
[package]
name = "react_actix"
```

We don't need any other files made from Rust besides this one.

We also have some static files for React frontend code to work at **public** folder.

In short, you just need to figure out how to include them in a Docker image that we will doing soon.

1. **react_actix**
2. **public** folder

There is no difference between using these commands on your console or testing them on your machine separately.

```console
$mkdir Docker
$cp react_actix Docker
$cp -R public Docker
$mv Docker && ./react_actix
```

<br />

## 3. Make a Docker image with Dockerfile

In the previous section, we learned which files to include in your Docker container.

What remains is to decide which **Docker image** base to use and write a few commands in Dockerfile to make everything work.

As this is the Rust project, it is easy to think that we are going to need [the Rust official image from the Dockerhub](https://hub.docker.com/_/rust).

But, we don't need that. We can just execute the **react_actix** binary project with **$./react_actix** inside a Docker container.

Compare the results.

1. Whatever OS you use in your machine.

```json
{
    "ubuntu": "64.2MB",
    "with_project": "78.4MB"
}
```

2. With Rust official Docker image.

```json
{
    "rust": "1.62GB",
    "with_project": "1.64GB"
}
```

Now that we already know the difference, we will finally make a **Dockerfile**. It is just a file to include commands we used manually before in [How to use Docker commands] Steadylearner previous post.

**It will make a Docker image, not a container** and it will be similar to this:

```toml
FROM ubuntu # 1.
WORKDIR /app # 2.
# 3.
COPY ./public /app/public
COPY ./target/release/react_actix /app
EXPOSE 8000 # 4.
CMD ["./react_actix"] #5.
```

The process consists of making a virtual development environment similar to your machine with Docker. So, you help it to execute some commands that we read in the previous section.

**1.** Use OS or base Docker image you want.

**2.** Rename your project as you want.

**3.** We need static files for React frontend and an executable binary file for **Rust**.

You will only need **COPY ./target/release/react_actix /app** if your project doesn't have the frontend code.

**4.** Expose the port you use in your project outside of your docker container.

Easily verify using **$curl localhost** later.

**5.** We execute the Rust web app with this. There is nothing complicated here.

When **Dockerfile** is ready, make a **./docker-build.bash** file, similar to this to create a Docker image.

```bash
docker build -t youraccount/rust .
```

Execute it with **./docker-build.bash**. It won't be long before Docker builds the image.

Then, verify the result with **$docker images**. It will be similar to this:

```console
REPOSITORY              TAG
youraccount/rust          latest
```

<br />

## 4. Verify the result in your machine

Preparation ends here. Make the Docker container from the image you made with this command:

```console
$docker run -d -p 80:8000 steadylearner/rust
```

To verify if the Docker container was made from the image, use **$docker ps -a**.

The message displayed will look like this:

```console
COMMAND        PORTS
"./react_actix"    0.0.0.0:80->8000/tcp
```

We exposed **8000** port in the previous part. The result of it is the **8000/tcp** above.

Then, we used port mapping with **-p 80:8000** to test it easily with: **curl**. You can check if it worked using this:

```console
PORTS
0.0.0.0:80->8000/tcp
```

Everything is ready now! Use **$curl localhost** or visit it in your browser. Refresh it at **/**. Use **/user** to verify that the Rust server side code is working well.

To push this to DockerHub, just use a command similar to **$docker push youraccount/rust**.

If you want to use GitHub, the process is similar. But first, make sure you have an account and a repository.

<br />

## 5. Conclusion

I hope you made it all work fine! We learned how to dockerize Rust web app.

We used React production files to make it more meaningful. You could use any static files or just dismiss them.

In the later [Rust blog posts], we will learn how to deploy Rust Docker images to **AWS**. We have many examples already at [React Rust], so it won't be difficult.

Finding a working Rust code isn't easy. I use Express and other frameworks to prototype the projects and to write blog posts. If you want to experiment, check [React Rust] repository.

Stay on top of the latest Steadylearner content: follow me on [Twitter] or star [Rust Full Stack].

Do you need **a Full Stack Developer who can use Docker and AWS to deploy the projects**? Contact me with [LinkedIn] or [Twitter] and I will help you.

You can invite me to work with your team. I can learn fast if there is a reason for that.

**Thank you! Share this post with others and help us to grow and improve.**
