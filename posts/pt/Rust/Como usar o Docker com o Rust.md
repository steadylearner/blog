<!--
    Post{
        subtitle: "Learn how to dockerize Rust web projects."
        image: "post/Rust/docker-rust.png",
        image_decription: "Image by Steadylearner",
        tags: "How Docker Rust Actix",
    }
-->

<!-- Link -->

[comment]: # (Os seguintes links não estão funcionando (Erro "página não encontrada) [React Rust]. Eu exclui os links que não estavam sendo utilizados)
[Steadylearner]: https://www.steadylearner.com
[Blog Steadylearner]: (https://www.steadylearner.com/blog)
[Rust Full Stack]: https://github.com/steadylearner/Rust-Full-Stack
[React Rust]: https://github.com/steadylearner/React-Rust

<!-- / -->

<!-- Steadylearner Post -->

[Posts sobre Rust]: https://www.steadylearner.com/blog/search/Rust
[Como usar comandos Docker]: https://www.steadylearner.com/blog/read/How-to-use-Docker-commands
[Como usar React com Rust Actix]: https://www.steadylearner.com/blog/read/How-to-use-React-with-Rust-Actix

<!-- / -->

<!-- Steadylearner Twitter and LinkedIn  -->

[Twitter]: https://twitter.com/steadylearner_p
[LinkedIn]: https://www.linkedin.com/in/steady-learner-3151b7164/

<!--  -->

Neste post, nós aprederemos como usar o [Docker][Como usar comandos Docker] com web frameworks Rust.

Traduzido e revisado por [Mariana Santos](https://www.linkedin.com/in/mariana-santos-89234a189/?locale=en_US)

Já temos o código do servidos React e Rust prontos em [React Rust]. Clone o repositório ou use o Docker para ver os resultados primeiro.

```console
$docker run -d -p 80:8000 steadylearner/rust && curl localhost
```

E então, o resultado será semelhante a este:

[![user-signup](https://www.steadylearner.com/static/images//post/React/user-signup.png)][React Rust]

<br />

<h2 class="red-white">[Pré-requisitos]</h2>

1. Docker
2. Rust

---

Eu presumo que você já esteja familiarizdo com o Docker e o Rust. Se você não usou comandos Docker, por favor, leia este post do [Steadylearner][Como usar comandos Docker] para mais informações.

<br />

<h2 class="blue">Índice</h2>

1. Atinja **0.0.0.0:** para usar seus projetos com Docker
2. Prepare seus arquivos
3. Faça uma imagem Docker usando o Dockerfile
4. Verifique o resultado em seu computador
5. Conclusão

---

<br />

## 1. Atinja **0.0.0.0:** para usar seus projetos com Docker

Talvez esta seja a etapa mais simples, porém também a mais importante para economizar seu tempo: verifique o código do servidor. Em server/actix/src/main.rs disponível em [React Rust], você verá um código parecido com este:

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

**Nunca, jamais** use **localhost** ou **127.0.0.1** etc. **Apenas** use portas que comecem com **0.0.0.0:** se você quiser colocar o seu projeto em um container Docker depois. Isto também serve para outros frameworks da web.

A porta **8000** é usada aqui para sincronizar com o frontend do React com o Webpack. Caso você queira implantar ele no AWS, use a porta 80 no lugar. Nós aprenderemos como fazer isso em outros [Posts sobre Rust].

Para mais informações sobre o código usado aqui, por favor, leia este post do Steadylearner [Como usar React com Rust Actix].

<br />

## 2. Prepare seus arquivos

Rust é uma linguagem compilada. Quando você usa **cargo run --release**, um arquivo binário executável é criado em **target/release/**.

Este arquivo terá o mesmo nome do seu projeto em **Cargo.toml** e será **react_actix**.

```toml
[package]
name = "react_actix"
```

Nós não precisamos de quaisquer outros arquivos feitos pelo Rust além deste.

Nós também temos alguns arquivos estáticos para o código do frontend do para funcionar na pasta **public**.

Resumidamente, você só precisa descobrir como os incluir na imagem Docker image que estaremos fazendo em breve.

1. **react_actix**
2. pasta **public**

Não há diferença entre usar estes comandos em seu console ou os estar separadamente no seu computador.

```console
$mkdir Docker
$cp react_actix Docker
$cp -R public Docker
$mv Docker && ./react_actix
```

<br />

## 3. Faça uma imagem Docker usando o Dockerfile

Na seção anterior, nós aprendemos sobre quais arquivos incluir no seu container Docker.

O que nos resta é decidir qual base de **imagem Docker** usar e digitar alguns comandos em um Dockerfile para fazer tudo funcionar.

Como isto é um projeto Rust, é fácil imaginar que nós iremos precisar da [imagem oficial do Rust do Dockerhub](https://hub.docker.com/_/rust).

Mas na verdade, nós não precisamos disso. Nós podemos apenas executar o projeto binário **react_actix** com **$./react_actix** dentro de um container Docker.

Compare os resultados.

1. Qualquer que seja o sistema operacional que você use.

```json
{
    "ubuntu": "64.2MB",
    "with_project": "78.4MB"
}
```

2. Com a imagem oficial do Rust:

```json
{
    "rust": "1.62GB",
    "with_project": "1.64GB"
}
```

Agora que já sabemos a diferença, vamos finalmente fazer um **Dockerfile**. É apenas um arquivo para incluir os comandos que usamos manualmente no post [Como usar comandos Docker] anteriormente no [Blog Steadylearner].

**Isso fará uma imagem Docker, não um container** e será semelhante a isto:

```toml
FROM ubuntu # 1.
WORKDIR /app # 2.
# 3.
COPY ./public /app/public
COPY ./target/release/react_actix /app
EXPOSE 8000 # 4.
CMD ["./react_actix"] #5.
```

O processo consiste em fazer um ambiente de desenvolvimento virtual semelhante à seu computador usando o Docker. Então, você o ajuda a executar alguns dos comandos que nós lemos na seção anterior.

**1.** Use o sistema operacional ou a imagem base do Docker de sua escolha.

**2.** Renomeie seu projeto como quiser.

**3.** Precisamos de arquivos estáticos para o front-end do React e um arquivo binário executável para o **Rust**.

Você precisará apenas de **COPY ./target/release/react_actix /app** se o seu projeto não possuir frontend.

**4.** Exponha a porta que você usa em seu projeto fora do container Docker.

Verifique facilmente utilizando **$curl localhost** depois.

**5.** Nós executamos o web app Rust com isto. Não há nada complicado aqui.

Quando o **Dockerfile** estiver pronto, faça um arquivo **./docker-build.bash**, semelhante a isto para criar uma imagem Docker.

```bash
docker build -t youraccount/rust .
```

Execute usando **./docker-build.bash**. Não demorará muito para que o Docker construa a imagem.

Então, verifique o resultado com **$docker images**. Será parecido com isto:

```console
REPOSITORY              TAG
youraccount/rust          latest
```

<br />

## 4. Verifique o resultado em seu computador

A preparação acaba aqui. Faça o container Docker a partir da sua imagem usando este comando:

```console
$docker run -d -p 80:8000 steadylearner/rust
```

Para verificar se o container Docker foi criado a partir da imagem, use **$docker ps -a**.

A mensagem exibida será parecida com isto:

```console
COMMAND        PORTS
"./react_actix"    0.0.0.0:80->8000/tcp
```

Expusemos a porta **8000** na seção anterior. O resultado disso é o **8000/tcp** mostrado acima.

Então, usamos o mapeamento de porta com **-p 80:8000** para testar facilmente com: **curl**. Você pode verificar se funcionou usando isto:

```console
PORTS
0.0.0.0:80->8000/tcp
```

Está tudo pronto agora! Use **$curl localhost** ou acesse usando seu navegador. Atualize usando **/**. Utilize **/user** para checar se o código do servidor do Rust está funcionando bem.

Para enviar para o Docker Hub, basta usar um comando semelhante a: **$docker push youraccount/rust**.

Se você quiser usar o GitHub, o processo é semelhante. Mas primeiro, se certifique de ter uma conta e um repositório.

<br />

## 5. Conclusão

Espero que você tenha feito tudo funcionar bem! Nós aprendemos a dockerize um web app Rust.

Nós usamos arquivos de produção React para deixar mais significativo. You could use any static files or just dismiss them. Você pode usar qualquer arquivo estático ou simplesmente os descartar.

Nos próximos [Posts sobre Rust], nós aprederemos como implantar imagens Docker Rust para **AWS**. Nós temos muitos exemplos preparados em [React Rust], então não será complicado.

Encontrar um código Rust funcional não é nada fácil. Eu uso o Express e outros frameworks para fazer o protótipo dos projetos, e também os uso para escrever posts para blog. Caso deseje experimentar, verifique o repositório [React Rust].

Fique por dentro do conteúdo mais recente do [Steadylearner]: me segue no [Twitter] ou acesse [Rust Full Stack].

Você precisa de um Desenvolvedor de Full Stack Rust que consegue can implantar projetos com Docker, AWS etc? Entre em contato comigo pelo LinkedIn e eu o ajudarei.

Muito obrigado! Compartilhe este post com outros e nos ajude a crescer e melhorar.

Você pode me convidar para trabalhar com sua equipe. Posso aprender rápido, desde que haja um motivo para isso.
