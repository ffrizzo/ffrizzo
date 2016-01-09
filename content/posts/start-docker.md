+++
Categories = ["SO", "Docker", "DevOps"]
Description = ""
Tags = ["docker", "devops"]
author = "Fabiano Frizzo"
date = "2016-01-02T14:35:08-02:00"
socialsharing = true
title = "Inciando com Docker"
+++

Após um longo periodo sem atualizações estou de volta para falar sobre algo muito interessante que na verdade ja deveria ter escrito aqui sobre isso a muito tempo. Como este é o primeiro post  sobre [Docker](http://docker.com) vou começar com a instalação e uso do Docker no ambiente de desenvolvimento e em posts futuros vamos falar sobre algumas ferramentas e o uso do Docker em ambiente de produção.

Vou demonstrar a instalação do Docker em uma maquina com OsX. A instalação é diferente de uma maquina com Linux mas o a execução e uso é parecido. Eu sempre instalo tudo utilizando o [Homebrew](http://brew.sh) então com o Docker não poderia ser diferente.

Vamos precisar instalar o Docker e o [Docker Machine](https://docs.docker.com/machine/) para quem utiliza OsX ou Windows o Docker Machine é obrigatório pois precisamos criar uma maquina aonde possamos rodar os comandos Docker. Apesar de não ser obrigatório o uso do Docker Machine no Linux você também pode usar ele. O Docker Machine não só nos permite criar hosts locais para o Docker como também na Amazon na Digital Ocean entre outros.

Instalando o docker e o docker-machine no OsX
```
brew install docker
brew install docker-machine
```

Agora criaremos um host utilizando o docker-machine para que possamos executar os comandos docker, criar nossos containers, imagens etc.
```
docker-machine create -d virtualbox mydockermachine
```

Este comando adiciona algumas informações ao profile. Estas informações informam ao docker em que host os comandos deverão ser executados
```
eval "$(docker-machine env dev)"
```

Abaixo um exemplo do profile da minha maquina.
```
DOCKER_HOST=tcp://192.168.99.100:2376
DOCKER_MACHINE_NAME=dev
DOCKER_TLS_VERIFY=1
DOCKER_CERT_PATH=/Users/fabianofrizzo/.docker/machine/machines/dev
```

Se executarmos o comando abaixo teremos a lista de maquinas criadas e também qual esta ativa.
```
docker-machine ls
```

Fazendo pull das imagens para criar os containers. Podemos simplesmente fazer o pull da ultima versão(***latest***) como também podemos informar qual versão gostariamos de  fazer pull, para isto bastaria adicionar *:{versão}* logo após o nome da imagem que esta sendo feito o pull.
```
docker pull postgres
```

Criaremos o container docker apartir da imagem que baixamos no passo anterior.
```
docker run --name postgres -p 5432:5432 -e POSTGRES_PASSWORD={seu password} -d postgres
```
Um pouco sobre os parametros utilizados

* O parametro ***-p 5432:5432*** é para redirecionar a porta do container para a maquina host
* O ***-d*** é para rodar o container em background
* O ***--name postgres*** define um nome para este container
* E o ***-e POSTGRES_PASSWORD={seu password}*** define a senha do usuário principal do postgres

Se tudo occoreu como o esperado quando for executado o comando **docker ps** deverá aparecer na lista um container com o nome postgres, para visualizar todos os containers mesmo os que não estão sendo executados use o comando **docker ps -a**.
Se vocÊ precisar acessar o bash do seu container é possivel utilizando o comando **exec**. Com o comando **exec** é possível executar todos os comandos de um terminal linux você pode rodar ***ls***, ***rm***, etc.
```
docker exec -it postgres bash
```
O comando acima nos da acesso ao terminal do container postgres que criamos.

É possível parar e startar novamente o mesmo container com os comandos ***start*** e ***stop***

```
docker stop postgres
docker start postgres
```

Excluir um container
```
docker rm postgres
```

Excluir uma imagem.
```
docker rmi postgres
```

Para ver todos os comandos disponíveis no docker execute **docker help** e para mais detalhes sobre cada comando **docker help {comando}**

Este é um post básico sobre o uso do docker, em posts futuros irei abortar outros comandos do docker e outras ferramentas como **docker compose** e o **docker swarm**, claro que ainda temos várias outras ferramentas de terceiros para utilizar em conjunto com o docker e tornar o uso muito dele mais prático.
