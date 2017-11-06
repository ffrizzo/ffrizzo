+++
Categories = ["Supervisord", "devops"]
Description = ""
Tags = ["supervisord", "devops"]
author = "Fabiano Frizzo"
date = "2015-09-11T17:23:01-03:00"
socialsharing = true
title = "Controlando processos com o Supervisor"
slug="supervisor"
+++

[Supervisor](http://supervisord.org) é uma aplicação cliente/servidor que permite o usuário monitorar e controlar varios processos em sistemas baseados em UNIX. Ele ajuda a eliminar a burocratica tarefa de escrever arquivos de inicialização.
Supervisor roda os aplicativos como subprocessos desta forma ele consegue identificar o real status da aplicação se algum erro ocorrer ele reinicia o processo automaticamente se assim for configurado.

Instalando e configurando o supervisor no OsX.
O processo de instalação é muito simples bastando instalar via  **pip**.

```
pip install supervisor
```

Eu adicionei o arquivo de configuração do supervisor em **/usr/local/etc/supervisord.conf** com as seguintes configurações que utilizo na minha maquina.

```
[unix_http_server]
file=/tmp/supervisor.sock
chmod=0766
chown=fabianofrizzo:staff

[inet_http_server]
port=7070
username=admin
password=1234

[supervisord]
logfile = /Users/fabianofrizzo/.supervisor/supervisord.log
logfile_maxbytes = 50MB
logfile_backups=10
loglevel = info
pidfile = /tmp/supervisord.pid
nodaemon = False
minfds = 1024
minprocs = 200
umask = 022
identifier = supervisor
directory = /tmp
nocleanup = true
childlogdir = /tmp

[supervisorctl]
serverurl = unix:///tmp/supervisor.sock

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[include]
files = /usr/local/share/supervisor/conf.d/*.conf
```

Se desejar você poder acesar os processos por uma aplicação web para isso será preciso configurar o nó **inet_http_server** username e password não são obrigatórios. A interface da aplicação é bem simples porém você consegue fazer todas as operações por ela.
![alt text](/imgs/posts/supervisor/supervisor.png)

O nó **include** é aonde configuramos uma pasta onde o supervisor deve ler os arquivos que contém as configurações das aplicações que desejamos monitorar/controlar, é possível ter vários arquivos *.conf* nesta pasta, isto é importante pois podemos manter as configurações para varias aplicações em arquivos separados de forma organizada.

Depois de configurarmos o **supervisor** vamos configura para que ele rode como daemon no OsX. Vamos criar um *plist* no seguinte caminho **/Library/LaunchDaemons/com.agendaless.supervisord.plist**, agora sempre que o computador for reiniciado o supervisor será inicializado, abaixo o conteúdo do arquivo.

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>KeepAlive</key>
    <dict>
        <key>SuccessfulExit</key>
        <false/>
    </dict>
    <key>Label</key>
    <string>com.agendaless.supervisord</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/bin/supervisord</string>
        <string>-n</string>
        <string>-c</string>
        <string>/usr/local/etc/supervisord.conf</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

Vamos iniciar o supervisor.
```
launchctl load /Library/LaunchDaemons/com.agendaless.supervisord.plist
```

Abaixo um exemplo de um dos meus arquivos *.conf* que definem as aplicações a serem controladas pelo **supervisor**

```
[program:awassess]
command=java -Dlogging.path=/var/log/ffrizzo/ -jar /path/app1.war -Xms32m -Xss512k
user=fabianofrizzo
autostart=false
autorestart=false
startsecs=10
startretries=3
stdout_logfile=/var/log/ffrizzo/app1-stdout.log
stderr_logfile=/var/log/ffrizzo/app1-stderr.log

[program:awdatamgt]
command=java -Dlogging.path=/var/log/ffrizzo/ -jar /path/app2.war -Xms32m -Xss512k
user=fabianofrizzo
autostart=false
autorestart=false
startsecs=10
startretries=3
stdout_logfile=/var/log/ffrizzo/app2-stdout.log
stderr_logfile=/var/log/ffrizzo/app2-stderr.log
```

No mesmo arquivo eu tenho a definição de um grupo o qual posso usar para subir as aplicações definidas no grupo em um comando unico.

```
[group:apps]
programs=app-1,app-2
priority=999
```

Agora vamos inciar as aplicações de forma individual.
```
supervisorctl start app-1
supervisorctl start app-2
```

Para inciar todas as aplicações utilizamos o start all. Este comando vai inciar todas as aplicações de todos os arquivos **.conf** que estiverem na pasta configurada para o supervisor ler.
```
supervisorctl start all
```

Quando temos grupos definidos podemos iniciar o grupo ao invés de iniciar cada aplicação de forma individual. Podemos ter vários grupos isso nos ajuda a evitar rodar o **start all**.
```
supervisorctl start apps:*
```

Para pararmos os processos é só substituir o **start** pelo **stop** seguindo o mesmo padrão.

Se em algum momento você precisar adicionar mais algum aplicativo a sua lista ou mesmo alterar alguma configuração de algum aplicativo que ja esta na lista basta mandar o supervisor ler as configurações novamente utilizando o seguinte comando.
```
supervisorctl reread
```

Com estas configurações e comandos temos uma configuração do supervisor completa e funcional.
