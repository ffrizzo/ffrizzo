+++
Categories = ["SO", "Docker"]
Description = ""
Tags = ["so","docker"]
author = "Fabiano Frizzo"
date = "2015-07-18T22:05:36-03:00"
socialsharing = true
title = "Rancher OS - Uma distribuição Linux para rodar containers Docker"

+++

[Rancher OS](http://rancher.com/rancher-os/) é uma distribuição Linux com pouco mais de 20mb feita para rodar containers [Docker](http://docker.com). Ela ja vem com o docker configurado basta iniciar uma maquina com Rancher OS na amazon ou até mesmo instalar ela em um computador pessoal para ter todo o poder do Docker consumindo pouco recurso da maquina.

Nesse meio não podemos esquecer do [CoreOS](https://coreos.com) que tem o mesmo proprósito do RancherOS, porém a sua imagem é um pouco maior que a do RancherOS.

Mas o propósito deste post é ensinar a instalar o RancherOS em uma maquina que você tenha em casa, aquela maquina que você não usa com frequência ou uma maquina antiga que você ja não usa mais pois o desempenho não é legal para rodar os pesados softwares que precisamos no mundo do desenvolvimento de software.

Mas vamos ao que realmente interessa a instalação do RancherOS.

Primeiramente vamos fazer o Download da versão mais recente em https://releases.rancher.com/os/latest/rancheros.iso e agora vamos criar um pendrive ou um cd bootavel com a imagem do RancherOS. Lembrando que estou utilizando Mac OsX como estação de trabalho então os paços descritos para a criação do pendrive com RancherOS será baseado neste sistema operacional.

Convertendo o **iso** para um **img**
```
hdiutil convert -format UDRW -o ~/Downloads/rancheros.img ~/Downloads/rancheros.iso
```

Insira o pendrive que será utilizado para a instalação do RancherOS.
Na lista de comandos abaixo estamos.
  * Pegando a lista de devices para saber qual o numero correspondente ao nosso pendrive
  * Desmontando o pendrive.(Lembre de substituir o N pelo numero que identifica o pendrive)
  * Criando o pendrive com a imagem do RancherOS
  * E por fim ejetamos o pendrive.

```
diskutil list
diskutil unmountDisk /dev/diskN
sudo dd if=~/Downloads/rancheros.img of=/dev/rdiskN bs=1m
diskutil eject /dev/diskN
```

Agora vamos instalar o RancherOS em nosa maquina.

Vamos precisar criar um arquivo chamado cloud_config.yml este arquivo é um descriptor para indicar algumas configurações para a instalação do RancherOS. Eu optei por criar o descriptor na minha estação de trabalho e adicionar ele ao pendrive bootavel.

```
#cloud-config
ssh_authorized_keys:
  - ssh-rsa (cole aqui a chave ssh)

rancher:
  network:
    dns:
      nameservers:
        - 8.8.8.8
        - 8.8.4.4
    interfaces:
      eth*: {}
      eth0:
        match: eth0
        address: 192.168.0.110/24
        gateway: 192.168.0.254
        mtu: 1460
      lo:
        address: 127.0.0.1/8
```

O descriptor que utilizei é muito simples.

* **ssh_authorized_keys** contém as chaves ssh que tem autorização para acessar a maquina que estamos configurando. Você pode ter N chaves autorizadas basta incluir um novo **ssh-rsa**
* no nó rancher definimos informações com o **DNS** que a maquina deve usar e as informações da interface de rede. Eu prefiro manter um ip fixo nesta maquina.

É possível configurar varias outras coisas mas estas são as necessárias para termos uma maquina rodando 100%. Na [documentação oficial](http://docs.rancher.com/os/configuration/) temos mais informações das outras opções que temos para configurar o RancherOS.

E para finalmente instalarmos o RancherOS vamos executar o seguinte comando(lembre-se todos os dados neste hd será perdido então se você precisa de algum documento faço um backup antes de prosseguir.).

```
sudo rancheros-install -c cloud_config.yml -d /dev/sda
```

Agora temos o RancherOS instalado em nossa maquina pronto para o uso.
Para acessar a maquina com utilize o seguinte comando.
```
ssh -i /path/chave-privada rancher@<ip-address>
```

A partir daqui você ja tem podemos executar os comandos do Docker. Rode o comando a baixo e você vera as informações sobre as versões do Docker
```
docker version
```

Eu tenho utilizado muito esta maquina com o RancherOS tenho varios containers rodando nela principalmente pela grande facilidade e rapidez de subir e parar os containers.
Em um próximo post vamos falar sobre o uso do [Docker](http://docker.com) no ambiente de desenvolvimento e de produção.
