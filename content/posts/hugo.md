+++
Tags = ["desenvolvimento", "golang"]
Categories = ["Desenvolvimento", "GoLang"]
date = "2015-06-02T00:03:58-03:00"
title = "Hugo - Gerando um site com conteúdo estático."
Description = ""
slug = "hugo"
author = "Fabiano Frizzo"
socialsharing = true
+++

Vamos falar um pouco sobre o [HUGO](http://hugo.spf13.com) uma das ferramentas que utilizei para a criação do blog.

O [HUGO](http://hugo.spf13.com) é um gerador de conteudo estático assim como o [Jekyll](http://jekyllrb.com) que é bem conhecido por quem utiliza o [GitHub Pages](https://pages.github.com) em ambos é utilizado MarkDown para criação das paginas as quais são convertidas em arquivo html com o conteúdo final.

Porque optiei pelo Hugo e não pelo Jekyll?
Esta escolha não foi tão difícil quanto parece, tenho estudado sobre [GO](http://golang.org) nos ultimos meses(8 para ser mais exato) e o HUGO é escrito em GO logo eu posso aprender mais sobre a linguage utilizando uma ferramenta que ja utiliza GO e ainda posso contribuir com correções de bugs que são abertos no [GitHub](http://github.com).

Como ja comentei antes o HUGO gera o conteúdo HTML apartir de arquivos escritos com MarkDown e utiliza templates para a criação do HTML isso torna a apresentação do conteúdo muito mais rapida pois o html final ja esta gerado só esperando para ser consumido pelo usuário, com isso diminuimos o custo da geração do html que inclui na maioria das vezes consultas ao banco de dados o retorno destes dados e a criação do html com o conteúdo, podemos citar como exemplo o [Wordpress](https://wordpress.com).

Mas vamos a parte mais interessante a instalação e utilização do HUGO.
Se você estiver no Linux ou Windows baixe o release apartir do link [https://github.com/spf13/hugo/releases](https://github.com/spf13/hugo/releases), mas se você esta no OsX muito provavel que tenha o [HomeBrew](http://brew.sh)(se ainda não tem esta é uma boa hora para começar a usar ele) você só precisa rodar o seguinte commando.

```
brew install hugo
```

Pensando que você instalou o HUGO corretamente vamos ter ele rodando pelo terminal. Para confirmar execute o comando a seguir e veja a ajuda do HUGO

```
hugo help
```

Vamos iniciar a criação de uma nova estrutura para o nosso site estático.

```
hugo new site ffrizzo
```

Neste comando dizemos para o hugo o que queremos criar um novo site na pasta ffrizzo no diretório corrente. E então teremos a seguinte estrutura.

```
  ├──ffrizzo
  |  ├── content
  |  ├── data
  |  ├── static
  |  └── themes
  └── config.toml
```

A pasta **content** é uma das mais importantes da app é nesta pasta aonde serão criados nossos arquivos markdown que o HUGO ira utilizar para a criação do html final, cada arquivo markdown tera o seu arquivo html. A pasta **data** é aonde adicionamos arquivos no formato YAML/JSON/TOML para serem usados na geração do html essas informações ficaram disponíveis no template através de um MAP no atributo DATA.

```
{{ range $.Site.Data.state }}
  {{ partial "state.html" . }}
{{ end }}
```
E no state.html tera algo semelhante ao que temos abaixo.

```
<ul>
  {{ range .city }}
  <li>{{ . }}</li>
  {{ end }}
</ul>
```

A pasta **static** é para servir todos os arquivos estáticos(js/css/imgs/etc) que você deseja adicionar a aplicação, ela não tem um estrutura interna definida logo é possível criar a sua própria estrutura e o HUGO vai tratar de utilizar ela. A pasta **temes** é aonde você deve baixar os temas que o HUGO deve utilizar na geração do html. Você pode obter alguns temas para o hugo em https://github.com/spf13/hugoThemes. Ou também pode criar seu próprio tema que não é difícil.

Agora você precisa criar seus posts. Use o hugo para criar os arquivos markdown.

```
hugo new posts/welcome.md
```

Este comando vai gerar um arquivo welcome.md em content/posts/ com um metadao no inicio do arquivo com algumas definições a respeito do novo post. Você pode ver isso no repositório do GitHub(https://github.com/ffrizzo/ffrizzo) que eu utilio para este blog.

Agora vamos rodar o server do HUGO para visualizarmos como o nosso post esta ficando. O parametro --watch utilizado é para não precisarmos reiniciar o servidor a cada alteração que for feita em qualquer arquivo do projeto com exceção do **config.toml**.

```
hugo server --watch
```

Para gerarmos os arquivos finais para deployment rodamos somento o comando abaixo. Ele irá criar uma pasta  **public/** com todo o conteúdo que você precisa para fazer o deploy. Após isso é só fazer o deploy em algum servidor capaz de servir paginas html.

```
hugo
```

No próximo post eu vou falar sobre sobre a ferramenta que utilizei para automatizar o deploy do blog.
Eu só preciso criar novos posts e mandar um push para o GitHub.
