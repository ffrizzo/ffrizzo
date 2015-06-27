+++
Categories = ["Jboss","Java", "Sql", "P6Spy"]
Description = ""
Tags = ["jboss", "java", "sql", "p6spy"]
author = "Fabiano Frizzo"
date = "2015-06-27T17:08:46-03:00"
socialsharing = true
title = "JBoss/Wildfly - Configurando log de queries para o datasource"
+++

Muitas vezes quando estamos desenvolvendo precisamos verificar quais e como estão sendo geradas as queries pelo [Hibernate](http://hibernate.org), e para isso utilizamos as opções do próprio hibernate a **show_sql** e a **format_sql** porém estas opções nos mostram as queries com varias **?** no lugar dos parametros que estão sendo passados para a query assim dificultando um pouco saber se os parametros estão corretos ou não.

Utilizando o [JBoss AS](http://jbossas.jboss.org) ou o [Wildfly](http://wildfly.org) utilizando datasource nós podemos ativar o log do próprio datasource, este log é bem detalhado mostra as queries as transações em fim todo o o processo envolvendo o datasource. Mas também podemos utilizar o [P6SPY](https://github.com/p6spy/p6spy) integrado com os datasources abaixo vou explicar como utilizar as duas formas.

Utilizando o spy default do JBoss AS/Wildfly.
Para ativar o log do datasource precisamos editar o **standalone.xml** e adicionar o seguinte trecho as configurações de log.
```
<logger category="jboss.jdbc.spy">
    <level name="TRACE"/>
</logger>
```
Também precisamos habilitar o **spy** para o datasource que que desejamos adicionado **spy=''true''** a declaração do datasource.
```
<datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" use-java-context="true" enabled="true" spy="true">
```

Vamos analisar o log gerado pelo **jdbc-spy** do Jboss AS/Wildfly. Ainda assim temos a query gerada com **?** exemplo **(limit ?)**, porém podemos notar logo a seguir um **[PreparedStatement] setInt(1, 1)** aonde o primeiro parametro do setInt é a posição e o segundo o valor de fato, neste exemplo só temos uma parametro a ser setado o **limit**, mas a quantidade de PreparedStatement pode variar de acordo com a sua query dependendo da quantidade de where/and/order que tiver a query.
```
14:22:02,767 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] prepareStatement(select this_.cod as cod1112_1_, this_.descricao as descricao1112_1_, this_.desoneracaoFolha as desonera3_1112_1_, this_.tipo_empresa_cod_fk as tipo4_1112_1_, tipoempres2_.cod as cod1195_0_, tipoempres2_.descricao as descricao1195_0_ from global.cnae this_ inner join global.tipo_empresa tipoempres2_ on this_.tipo_empresa_cod_fk=tipoempres2_.cod order by this_.cod asc limit ?)
14:22:02,769 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [PreparedStatement] setInt(1, 1)
14:22:02,770 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [PreparedStatement] executeQuery()
14:22:02,822 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] next()
14:22:02,822 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] getInt(cod1195_0_)
14:22:02,822 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] wasNull()
14:22:02,822 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] getString(cod1112_1_)
14:22:02,823 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] wasNull()
14:22:02,823 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] getString(descricao1195_0_)
14:22:02,823 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] wasNull()
14:22:02,824 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] getString(descricao1112_1_)
14:22:02,824 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] wasNull()
14:22:02,824 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] getBoolean(desonera3_1112_1_)
14:22:02,824 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] wasNull()
14:22:02,825 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] getInt(tipo4_1112_1_)
14:22:02,825 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] wasNull()
14:22:02,826 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] close()
14:22:02,826 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Statement] getMaxRows()
14:22:02,826 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Statement] getQueryTimeout()
14:22:02,826 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Statement] close()
14:22:02,827 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] isClosed()
14:22:02,827 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] getWarnings()
14:22:02,827 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] clearWarnings()
14:22:02,828 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] close()
14:22:02,830 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] prepareStatement(select count(*) as y0_ from global.cnae this_)
14:22:02,831 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [PreparedStatement] executeQuery()
14:22:02,849 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] next()
14:22:02,849 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] getLong(y0_)
14:22:02,850 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] wasNull()
14:22:02,850 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] next()
14:22:02,850 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [ResultSet] close()
14:22:02,851 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Statement] getMaxRows()
14:22:02,851 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Statement] getQueryTimeout()
14:22:02,851 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Statement] close()
14:22:02,852 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] isClosed()
14:22:02,852 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] getWarnings()
14:22:02,852 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] clearWarnings()
14:22:02,852 DEBUG [jboss.jdbc.spy] (http-localhost-127.0.0.1-8080-2) java:jboss/datasource/exampleDS [Connection] close()
```

Apesar deste log me mostrar a query final gerada e os parametros sendo usados ainda assim não parecia o ideal para mim, imagine que você tenha uma query com vários filtros teriamos varios PreparedStatement e se fosse preciso executar esta query diretamente no banco de dados precisariamos pegar cada PreparedStatement e substituir os valores nos parametros **?** da query.

Agora vou explicar como utilizar o [P6SPY](https://github.com/p6spy/p6spy) para gerar os logs das queries.
Para ativar o P6SPY no JBoss AS/Wildfly também é muito simples. Vamos precisar adicionar o arquivo **spy.properties** na pasta **bin/** do JBoss AS/Wildfly com duas informações.

<pre><code class="properties">append=true
realdatasource=java:jboss/datasource/exampleDS_real
</code></pre>

E no nosso arquivo **standalone.xml** vamos precisar adicionar um driver para o P6SPY e um datasource para ele. Vamos ter as configurações de driver e datasource da seguinte forma.

```
<subsystem xmlns="urn:jboss:domain:datasources:1.0">
        <datasource jta="true" jndi-name="java:jboss/datasource/exampleDS_real" pool-name="example_pool" enabled="true" use-java-context="true" spy="true" use-ccm="false">
            <connection-url>jdbc:postgresql://localhost:5432/example</connection-url>
            <driver>postgresql</driver>
            <pool>
                <min-pool-size>5</min-pool-size>
                <max-pool-size>1000</max-pool-size>
                <prefill>false</prefill>
                <use-strict-min>false</use-strict-min>
                <flush-strategy>FailingConnectionOnly</flush-strategy>
            </pool>
            <security>
                <user-name>postgres</user-name>
                <password>postgres</password>
            </security>
            <validation>
                <check-valid-connection-sql>SELECT 1</check-valid-connection-sql>
                <validate-on-match>false</validate-on-match>
                <background-validation>false</background-validation>
                <use-fast-fail>false</use-fast-fail>
            </validation>
        </datasource>
        <datasource jta="true" jndi-name="java:jboss/datasource/exampleDS" pool-name="exampleP6SPY_pool" enabled="true" use-java-context="true" use-ccm="true">
            <connection-url>jdbc:p6spy:postgresql://localhost:5432/example</connection-url>
            <driver>p6spy</driver>
            <security>
                <user-name>postgres</user-name>
                <password>postgres</password>
            </security>
        </datasource>
        <drivers>
            <driver name="postgresql" module="org.postgresql">
                <xa-datasource-class>org.postgresql.xa.PGXADatasource</xa-datasource-class>
            </driver>
            <driver name="p6spy" module="com.p6spy">
                <driver-class>com.p6spy.engine.spy.P6SpyDriver</driver-class>
            </driver>
        </drivers>
    </datasources>
</subsystem>
```
Desta forma estamos modificando para a nossa aplicação utilizar o DataSource com o P6SPY sem precisar alterar o código da nossa aplicação. E o P6SPY vai utilizar o DataSource real que configuramos no **spy.properties**.

Não podemos esquecer é claro de criar um module com o driver do P6SPY. A estrutura da pasta do P6SPY para o JBoss AS/Wildfly ficara da seguinte forma.

```
├── com
│   ├── p6spy
│   │   └── main
│   │       ├── module.xml
│   │       ├── p6spy-2.1.4.jar

```

E o arquivo **module.xml** tera o seguinte conteúdo.
```
<module xmlns="urn:jboss:module:1.0" name="com.p6spy">
    <resources>
        <resource-root path="p6spy-2.1.4.jar"/>
    </resources>
    <dependencies>
        <module name="javax.api"/>
        <module name="javax.transaction.api"/>
        <!-- make sure to refer to module holding real driver -->
        <module name="org.postgresql"/>
    </dependencies>
</module>
```
A partir de agora o P6SPY passara a gerar os logs na pasta **bin/spy.log** na mesma pasta que esta o **spy.properties** esta opção é configurável dentro do **spy.properties**.

Vamos analisar o log gerado pelo P6SPY.
Note que ainda temos a query original com **?** mas agora também temos a query com o parametro certo podendo ser copiada e executada diretamente no banco sem qualquer esforço extra.

```
1435425204636|9|statement|connection 1|select this_.cod as cod1112_1_, this_.descricao as descricao1112_1_, this_.desoneracaoFolha as desonera3_1112_1_, this_.tipo_empresa_cod_fk as tipo4_1112_1_, tipoempres2_.cod as cod1195_0_, tipoempres2_.descricao as descricao1195_0_ from global.cnae this_ inner join global.tipo_empresa tipoempres2_ on this_.tipo_empresa_cod_fk=tipoempres2_.cod order by this_.cod asc limit ?|select this_.cod as cod1112_1_, this_.descricao as descricao1112_1_, this_.desoneracaoFolha as desonera3_1112_1_, this_.tipo_empresa_cod_fk as tipo4_1112_1_, tipoempres2_.cod as cod1195_0_, tipoempres2_.descricao as descricao1195_0_ from global.cnae this_ inner join global.tipo_empresa tipoempres2_ on this_.tipo_empresa_cod_fk=tipoempres2_.cod order by this_.cod asc limit 1
```

Agora imaginemos uma query aonde temos uma clausa **where** complexa com varios **and's** com a query gerada pelo P6SPY seria muito mais rapido e pratico pegar esta query e analisar ou execar diretamente no banco de dados.
