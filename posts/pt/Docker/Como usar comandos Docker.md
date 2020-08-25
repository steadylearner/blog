<!--
    Post{
        subtitle: "Aprenda como usar comandos Docker e como enviar imagens para o Docker Hub"
        image: "posts/web/docker.png",
        image_decription: "Image from the official website",
        tags: "Como usar Docker code",
    }
-->

<!-- Link -->

[Steadylearner]: https://www.steadylearner.com
[Docker Website]: https://docs.docker.com/get-started
[Como instalar Docker]: https://www.google.com/search?q=how+to+install+docker
[Como implantar um container usando o Docker]: https://thenewstack.io/how-to-deploy-a-container-with-docker/
[Docker Curriculum]: https://docker-curriculum.com/
[Docker Hub]: https://hub.docker.com/
[Ciclo de vida do Docker]: (https://medium.com/@BeNitinAgarwal/lifecycle-of-docker-container-d2da9f85959).
[AWS]: https://aws.amazon.com
[Elastic Beanstalk]: https://aws.amazon.com/pt/elasticbeanstalk/
[ECS]: https://aws.amazon.com/ecs/
[Cloud​Formation]: https://aws.amazon.com/pt/cloudformation/
[Yarn]: https://yarnpkg.com/lang/en/
[Express]: https://expressjs.com/

<!-- / -->

<!-- Steadylearner Post -->

[Blog Steadylearner]: https://www.steadylearner.com/blog

<!-- / -->

<!-- Steadylearner Twitter and LinkedIn  -->

[Twitter]: https://twitter.com/steadylearner_p
[LinkedIn]: https://www.linkedin.com/in/steady-learner-3151b7164/

<!--  -->

Neste post, iremos aprender como usar comandos do Docker. Nós faremos um web app dentro de um container Docker, e depois o transformaremos numa imagem. Também aprenderemos como fazer uploads para o [Docker Hub].

<br />

<h2 class="red-white">[Pré-requisitos]</h2>

1. [Como instalar Docker]
2. [Docker Website], [Docker Curriculum]
3. [Como implantar um container usando o Docker], [Ciclo de vida do Docker]

---
Primeiro, você deve instalar o Docker, se você não o tem ainda. Digite **$docker** no seu computador e será mostrado como proceder com a instalação. Ou apenas faça uma busca no seu navegador sobre como instalar.

Este post é um resumo do [Docker Website], [Docker Curriculum] etc. Eu espero que você os leia primeiro, mas você não precisa dedicar tanto tempo a eles. Nós vamos aprender como implantar um web app e microserviços com o [AWS] em outros post do [Blog Steadylearner].

<br />

<h2 class="blue">índice</h2>

1. Confirme a instalação com Nginx
2. Configure seu ambiente de desenvolvimento Docker
3. Como mover seus arquivos e pastas locais para containers Docker
4. Como usar frameworks web com containers Docker
5. Como modificar portas de rede de imagens de Docker
6. Imagens e Conteiners de Docker
7. Como subir suas imagens Docker no Docker Hub
8. Conclusão

---

<br />

## 1. Confirme a instalação com Nginx

Eu espero que você consiga instalar o Docker. Antes de apredermos como cada comando do Docker funciona, vamos testar se estes conseguem ou não mostrar algum resultado no seu computador.

Use-os no CLI.

```console
$docker search nginx
$docker pull nginx
$docker run --name nginx-webserver -p 80:80 nginx
```

Então, acesse [localhost](http://localhost.com). Isto será exibido no seu navegador:

```console
Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

Em português

```console
Bem vindo ao nginx!

Se você vê esta página, o servidor nginx foi instalado com sucesso e está funcionando. É necessária configuração inicial.

Para documentação e suporte online, consulte nginx.org.
O suporte comercial está disponível em nginx.com.

Obrigado por usar o nginx.
```

Perceba que você precisa de apenas alguns comandos para utilizar o Docker. Você também poderia iniciar um container Docker com um nome específico e o executar com comandos bash:

```console
$docker run --name nginx-webserver -p 80:80 -d nginx
$docker exec -it CONTAINER_ID bash
```

<br />

## 2. Configure seu ambiente de desenvolvimento Docker

Nesta seção, vamos aprender como configurar uma imagem Docker padrão usando o Ubuntu. Você poderá reusar esta imagem depois. Caso você use outro sistema operacional, por favor, o utilize e consulte esta parte.

Comece com comandos **pull** para fazer o download da imagem o oficial do Ubuntu do [Docker Hub]. Se o Docker Hub for algo novo para você, você pode compará-lo com o GitHub, por causa dos repositórios que ambos as plataformas possuem.

```console
$docker pull ubuntu
```

Agora, faça um container no seu computador. Para baixar minimal softwares, use comandos sh ou bash com isto:

```console
$docker run -it ubuntu sh
```

Comece instalando **CURL** para baixar outros programas.

```console
$apt-get update
$apt-get install curl
$curl https://www.steadylearner.com
```

Se você estiver fora do container, reinicie-o com isto:

```console
$docker exec -it CONTAINER_ID bash
```

Você pode encontrar o CONTAINER_ID com **docker ps -a**. Este é um comando que você usará com frequência e ele lhe mostrará alguns metadados úteis dos container Docker.

Nós vamos fazer um web app Node simples, "Hello, World", como exemplo para neste post. Vamos começar configurando o ambiente de desenvolvimento Node. Siga estes passos caso você queira usar o mesmo projeto deste post.

Ou, em vez disto, você também pode usar [$docker run -d steadylearner/ubuntu_node](https://cloud.docker.com/u/steadylearner/repository/docker/steadylearner/ubuntu_node).

Você deve estar dentro do container Docker para os utilizar

<details>
  <summary class="red-white font-normal hover cursor-pointer transition-half">Node, NPM, Yarn</summary>

```console
curl -sL https://deb.nodesource.com/setup_12.x | bash
```

Isto será exibido:

```console
## Run `sudo apt-get install -y nodejs` to install Node.js 12.x and npm
## You may also need development tools to build native addons:
    sudo apt-get install gcc g++ make
## To install the Yarn package manager, run:
    curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
    sudo apt-get update && sudo apt-get install yarn
```

Você deve usar comando sem sudo.

```console
apt-get install gcc g++ make
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

apt-get update && apt-get install yarn
```

Siga estes comandos e instale tudo.

Teste o funcionamento do Node com isto:

```console
$node
$console.log("Hello from www.steadylearner.com");
```

</details>

<details>
  <summary class="red-white font-normal hover cursor-pointer transition-half">Vim</summary>

Use este comando com --assume-yes ou -y para pular perguntas de instalação.

```console
apt install --assume-yes vim
```

O editor de texto Vim será instalado. Agora, use este comando para o utilizar:

```console
$vim hello.js
```

Modifique seu arquivo hello.js com isto e digite **:wq** para salvar e sair do Vim.

```js
// hello.js
console.log("Hello from www.steadylearner.com");
```

Use este comando para verificar se o Node está instalado corretamente:

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

Isto irá instalar o Git e verificar se está instalado e em qual versão:

```console
$git --version
```

Então, use o nome de usuário e o email do GitHub do seu computador:

```console
$git config --get user.name
$git config --get user.email
```

Use-os no container Docker para operar o Git:

```console
$git config --global user.name yourname
$git config --global user.name youremail
```

Faça uso do mesmo comando (--get) antes de os checar em seu container Docker.

Testes o funcionamento do clone do Git para fazer o download de arquivos de seus repositórios no GitHub. Por examplo, clone estes repositórios:[steadylearner/docker-examples](http://github.com/steadylearner/docker-examples) usando isto:

[comment]: # (Apenas adicionei um link para o repositório)

```console
$git clone https://github.com/steadylearner/docker-examples.git
```

</details>

Eu espero que você consiga instalar tudo que achar necessário em seus containers Docker.

Você pode pular esta parte sobre o [Yarn] e usar comandos npm padrões, se preferir. Caso contrário, leia [este post](https://linuxize.com/post/how-to-install-yarn-on-ubuntu-18-04/) para mais informações.

Primeiro, verifique a versão do [Yarn].

```console
$yarn -v
```

A versão do seu Yarn será exibida.

Em seguida, use estes comandos para usar o projeto Node:

```console
$cd /home
$mkdir node && cd node
$yarn init
$yarn add chalk
```

Teste o funcionamento do Yarn ou do NPM com módulos NPM com isto:

```js
// Start with $node in your console and use each command.

const chalk = require("chalk");
const blue = chalk.blue;
const hello = blue("Hello from www.steadylearner.com");
console.log(hello);
```

A mensagem **Hello from www.steadylearner.com** deverá aparecer no seu computador.

Nós verificamos o trabalho dos pacotes NPM em seu container Docker com isto.

[comment]: # (Há algo faltando aqui?)

Se preferir, faça alias para esse repositório também similar a isto.

```console
$vim ~/.bashrc
```

Digite isto e :wq para salvar e sair:

```bash
alias work="cd /home/node"
```

Use **$source ~/.bashrc** e você poderá usar seu **$work** quando quiser. Depois, você também pode definir o WORKDIR usando **Dockerfile** ou **docker-compsose.yml**.

Haverá muitos containers Docker no seu computador. Use estes comandos para excluir os que forem desnecessarios:

**1.** Liste e remova containers Docker antigos.

```console
$docker ps -a
```

A lista de instâncias que você executou antes será exibida.

**2.** Exclua aqueles que você não precisa

```console
$docker stop containerid
$docker rm containerid
```

or

```console
$docker rm containerid -f
```

<br />

## 3. Como mover seus arquivos e pastas locais para containers Docker

Nós podemos usar comandos Git para baixar arquivos do GitHub em seus containers. Além disso, você pode usar comandos Docker para mover seus arquivos e pastas locais para containers Docker e vice versa.

Consulte estes exemplos ou **docker cp --help**.

**1.** Arquivos

```console
$docker cp from_localhost.txt containerid:/from_localhost.txt
$docker cp containerid:/from_docker from_docker.txt
```

**2.** Pastas

```console
$docker cp from_localhost containerid:/from_localhost
$docker cp containerid:/from_localhost from_localhost
```

<br />

## 4. Como usar frameworks web com containers Docker

Nós instalamos softwares relevantes do Node para esta parte. Se você usar frameworks web em outras linguagens, por favor, se refira apenas ao fluxo de trabalho desta seção.

<details>
  <summary class="red-white font-normal hover cursor-pointer transition-half">Express</summary>

Instale as dependências que nós usaremos dentro do container Docker com isto:

```console
$yarn add express chalk
```

Depois, nós construiremos um aplicativo "Hello, World!" usando o código JavaScript abaixo.

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

Mas usar **$curl http://localhost:3000** ou visitar em seu navegador não funcionará ainda.

Cada container tem seu próprio IP para convesara com outras redes. Nós devemos inspecionar o container Docker com **$docker inspect CONTAINER_ID > inspect.txt**.

Você pode encontrar o IP no final do inspect.txt and e será parecido com **172.17.0.2**. Economize seu tempo usando getIP.js e **$node getIP.js**.

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

Você pode utilizar o [comando de inspeção do Docker](https://docs.docker.com/engine/reference/commandline/inspect/) também.

Teste o IP usando $curl http://172.17.0.2:3000/ ou verifique usando seu naveagdor.

Se você conseguir visualizar esta mensagem, perceba que você realmente pode desenvolver aplicações da web em seu computador usando o Docker.

```console
Hello, World!
```

</details>

<br />

## 5. Como modificar portas de rede de imagens de Docker

Na seção anterior, nós tivemos que descobrir as portas de rede para o framework da web poder acessar. Como alternativa, [você pode começar com sua porta customizada](https://www.google.com/search?&q=how+to+assign+port+for+docker+container).

```console
$docker run -it --name ubuntu_node -p 80:80 ubuntu
```

Você também poder usar isto com **-d** para [fazer o container rodas em segundo plano](https://docs.docker.com/engine/reference/run/).

```console
docker run -d --name ubuntu_node -p 80:80 ubuntu:latest
```

Para entender melhor o que acontece aqui, consulte isto:

'Seguindo um padrão, a porta no host (container) é mapeada para 0.0.0.0, o que significa todos os endereços IP. Você pode especificar um determinado IP ao definir o mapeamento da porta, por exemplo, -p 127.0.0.1:80:80 '

<br />

## 6. Imagens e Containers Docker

Você pode ficar um pouco confuso acerca da diferença entre container e imagem Docker. Apenas pense nas imagens como as classes, e nos containers como as instâncias que você está usando em seu computador. Você pode:

1. Empurrar or executar (empurrar e iniciar) imagens and fazer containers Docker a partir delas em seu computador.

2. Editar arquivos em seus containers com **$docker exec -it containername bash**.

3. Fazer imagens dos containers com **$docker commit containername YourDockerHub/image && docker push account/image**.

Sinta-se livre para iniciar com um Dockerfile em vez dos passos **1.** e **2.** e enviar suas imagens Docker também. Nós aprenderemos mais sobre isso noutra postagem do [Blog Steadylearner] usando [Elastic Beanstalk].

<br />

## 7. Como enviar suas imagens Docker para o Docker Hub

Agora, nós iremos aprender como [criar um repositório no Docker Hub](https://cloud.docker.com/repository/create) utilizando o examplo que fizemos.

Primeiro, faça o login usando este comando:

```console
$docker login
```

Então, use [$docker commit](https://www.scalyr.com/blog/create-docker-image).

```console
$docker commit ubuntu_node
```

Em seguida, verifique a imagem feita a partir do container ubuntu_node usando isto:

```console
$docker images
```

Dê uma tag (nome) ao arquivo.

```console
$docker tag imageid steadylearner/ubuntu_node
```

Ou, você pode executar este comando:

```console
$docker commit ubuntu_node steadylearner/ubuntu_node
```

Agora, você pode enviar sua imagem Docker para o Docker Hub:

```console
$docker push steadylearner/ubuntu_node // yourusername/image
```

Aguarde o processo de upload se completar e use isto:

```console
$docker run -it steadylearner/ubuntu_node bash
```

Caso queira editar, apenas siga os mesmos passos que usamos antes.

Reinicie os containers com isto, caso eles parem:

```console
$docker restart containerid
$docker exec -it containerid bash
```

Para excluir um container feito a partir da imagem steadylearner/ubuntu_node ou uma imagem sua, você pode utilizar isto:

```console
$docker stop ubuntu_node
$docker container rm ubuntu_node
$docker image rm ubuntu
```

Se quiser renomear o seu container, use isto:

```console
$docker container rename randomname ubuntu_node
```

Use o seu ao invés de ubuntu_node ou steadylearner/ubuntu_node.

Se você modificar o project, use comandos similares a este:

```console
$docker commit ubuntu_node steadylearner/ubuntu_node
```

Ou com uma mensagem commit.

```console
$docker commit --message "Test message and will be similar to github -m option" ubuntu_node steadylearner/ubuntu_node
```

Agora, vamos enviar a imagem feita disto para o Docker Hub:

```console
$docker push steadylearner/ubuntu_node
```

e use isto:

```console
$docker run -it steadylearner/ubuntu_node bash
```

ou isso aqui, para verificar o resultado:

```console
$docker history steadylearner/ubuntu_node
```

<br />

## 8. Conclusão

Eu espero que você tenha feito tudo funcionar. Nós apredemos como instalar o Docker e como o fazer funcionar com o Nginx, e como fazer um container e uma imagem Docker e os enviar para o [Docker Hub].

Ainda existem muitas coisas para aprender. Mas tudo será mais fácil com exemplos. Nós próximos posts do [Blog Steadylearner], nós aprenderemos como implantar um web app usando [Elastic Beanstalk] do [AWS] e Dockerfile. Nós também vamos aprender como implantar microsserviços com [ECS], [Cloud​Formation], **docker-compose.yml** etc. Então, fique de olho nas atualizações!

Fique por dentro dos conteúdos mais recentes do Steadylearner: me segue no [Twitter].

Você precisa de **um Desenvolvedor de Full Stack Rust** que consegue can implantar projetos com Docker, AWS etc? Entre em contato comigo pelo [LinkedIn] e eu o ajudarei.

**Muito obrigado! Compartilhe este post com outros e nos ajude a crescer e melhorar.**

Traduzido e revisado por [Mariana Santos](https://www.linkedin.com/in/mariana-santos-89234a189/?locale=en_US)

[comment]: # (Tudo bem me identificar aqui certo? :)

Consulte estes comandos se você quiser mais. Use o ID ou o nome do containers.

### Logs do container

```console
$docker logs containerid | name
```

### Histórico da imagem

```console
#docker history steadylearner/ubuntu_node
```

### Exclua imagens inutilizadas

```console
$docker images
$docker image rm dockerimagename or docker rmi
```

### Renomeie o container

```console
$docker rename randomname whatyouwant
```

### Pause e execute novamente os containers

```console
$docker pause containerid | name
$docker ps -a
$docker unpuase containerid | name
$docker ps -a
```

### Inicie e pare os container

```console
$docker stop containerid | name
$docker ps -a
$docker start containerid | name
$docker ps -a
```

### Exclua containers

```console
$docker container rm containerid | name
```
