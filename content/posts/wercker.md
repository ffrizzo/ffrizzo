+++
Categories = ["Deployment", "GoLang", "Docker", "Wercker"]
Description = ""
Tags = ["deployment", "golang", "docker", "wercker"]
date = "2015-06-10T19:02:53-03:00"
title = "Wercker"
slug = "wecker"
+++

O que é o [Wercker](http://wercker.com)?

Wercker é uma plataforma para facilitar as tarefas de build e deployment. A versão atual é utiliza o [Docker](http://docker.com) para executar os builds, assim você pode usar o Wercker para fazer o build e deployment de qualquer linguagem basta utilizar uma imagem Docker e configurar os passos a serem executados pelo. Você pode customizar scripts para executar os passos tanto no build quanto no deployment assim evitando retrabalho quando precisar fazer o processo para outra aplicação que tenha a mesma estrutura. Não esqueça de  compartilhar estes scripts com a comunidade, para isso você vai precisar criar um repositório separado contendo um arquivo **run.sh** e um arquivo **wercker-step.yml**, o **wercker-step.yml** é o arquivo de manifesto ele vai conter as informações sobre o passo que você esta criando, o **run.sh** vai conter os comandos que o seu passo vai executar, e claro não podemos esquecer de adicionar um bom **README** a este reposiório.

Agora vamos a um exemplo. Vou utilizar o a configuração que fiz para o build e deployment do blog no [GitHub Pages](https://pages.github.com). Abaixo esta o arquivo **wercker.yml** completo.

```
box: google/golang
build:
  steps:
    - setup-go-workspace
    - script:
        name: setup hugo
        code: go get github.com/spf13/hugo
    - script:
        name: build app
        code: |
              cd /pipeline/source
              hugo
deploy:
  steps:
    - lukevivier/gh-pages:
        token: $GIT_TOKEN
        domain: blog.ffrizzo.com
        basedir: ./public

```

Como ja falei anteriormente o Wercker tem o Docker como base então podemos utilizar qualquer image Docker neste caso estou utilizando a imagem **google/golang** esta esta definida em **box: google/golang**.

Os passos para executar o build e criar os arquivos para deployment estão descritos em **build**.

Os Passos
* **step-go-workspace** configra todas as variáveis de ambiente para utilizaros GO
* **script setup hugo** aqui estamos baixando o **hugo** o comando **go get** vai baixar o hugo e todas as suas dependências e também irá fazer o build adicionando o hugo ao path.
* **script build app** aqui estamos acessando a pasta aonde o source code é baixado pelo wercker e em seguida rodamos o hugo para criar a pasta **public** que é utilizada para o deploy.

Segue um link para visualisar como os passos são executados no wercker https://app.wercker.com/#build/556e271f00bccd884301e4fa

O deployment
* **lukevivier/gh-pages** Este é um passo disponibilizado por outro desenvolvedor que estou utilizando. Eu preciso configurar o **TOKEN** o **DOMAIN** e o **BASEDIR**

  * **GIT_TOKEN** é uma variavel de ambiente que que configuramos nas settings do projeto. Mas esta mais especifamente esta configurada para estar disponível somento no momento em que os passos de deployment estão sendo executados.
  * **BASEDIR** é a pasta que será utilizada para o *deployment* no GitHub Pages.

Agora que temos o nosso projeto configurado com um arquivo **wercker.yml** vamos fazer as configurações no ambiente do Wercker.
Vamos criar uma nova aplicação
![alt text](/imgs/posts/wercker/wercker-create.png)

No primeiro passo vamos selecionar se o nosso repositório esta no GitHub ou no BitBucket.
![alt text](/imgs/posts/wercker/wercker-create-1.png)

O segundo passo qual o repositório que vamos utilizar.
![alt text](/imgs/posts/wercker/wercker-create-2.png)

Terceiro passo é selecionar o Owner.
![alt text](/imgs/posts/wercker/wercker-create-3.png)

Quarto passo selecionamos a forma como o wercker vai baixar o repositório.
![alt text](/imgs/posts/wercker/wercker-create-4.png)

No quinto passo o wercker vai tentar identificar qual linguagem esta sendo utilizada no repositório e vai criar um arquivo **wercker.yml** inicial.
![alt text](/imgs/posts/wercker/wercker-create-5.png)

O sexto e ultimo passo é para confirmar a criação da aplicação e decidir se você quer tornar esse repositório publico.
![alt text](/imgs/posts/wercker/wercker-create-6.png)

No passo a seguir vamos configurar onde faremos o deployment.
![alt text](/imgs/posts/wercker/wercker-deploy.png)

Vamos adicionar um novo deploy target do tipo **Custom Deploy**.
* Em **Deploy Target Name** vamos colocar GitHub Pages.
* Vamos selecionar o **Auto Deploy** assim toda vez que um novo commit for feito além do build também será executado os passos para deploy se o build ocorrer com sucesso. E também devemos indicar qual o branch será usado.

Vamos precisar adicionar uma variável com token para ser utilizado pelo deploy. ([GitHub Help Token](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)). Eu criei uma variável com o nome GIT_TOKEN e marquei ela como *Protected* assim ela não ficara visível.

E agora podemos alterar nosso código fazer pull para o repositório e deixar o Wercker fazer o resto do trabalho.

O Wercker também possui on cliente(OsX e Linux) que permitir baixarmos um container gerado por um build do wercker para rodar localmente com Docker. Isto é util quando precisamos descobrir porque nosso build não esta funcionando como deveria.

Para instalar o wercker no OsX.
```
curl https://s3.amazonaws.com/downloads.wercker.com/cli/stable/linux_amd64/wercker -o /usr/local/bin/wercker
```

No Linux
```
curl https://s3.amazonaws.com/downloads.wercker.com/cli/stable/linux_amd64/wercker -o /usr/local/bin/wercker
```

E por fim precisamos executar a seguinte linha tanto para OsX quanto para Linux.
```
chmod +x /usr/local/bin/wercker
```

Documentação http://devcenter.wercker.com/docs/index.html
Documentação da API http://devcenter.wercker.com/api/index.html
