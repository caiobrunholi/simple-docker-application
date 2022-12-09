# **TUTORIAL SIMPLES DE DOCKER**
## Tutorial utilizado:
- https://www.youtube.com/watch?v=Kzcz-EVKBEQ

Neste arquivo não será explicado o que é docker, os conceitos básicos ou aplicações. Para mais informações acesse a documentação do Docker (https://docs.docker.com/) e as anotações de @rogeriocassares (https://github.com/rogeriocassares/fullcycle-docker)
# Instalação do Docker
Primeiro é necessário instalar yuma vaersão do Docker, para isso siga o tutorial abaixo:
- Versão Ubuntu
    - https://docs.docker.com/engine/install/ubuntu/

Depois as instruções do *post install* para inserir as permissões necessárias:
- Versão Ubuntu
    - https://docs.docker.com/engine/install/linux-postinstall/
<br/><br/>
# Imagens Prontas
O Docker Hub (https://hub.docker.com/) possui diversas imagens prontas de linguagens e ambientes para usar (php, node, Mysql, etc)
<br/><br/>
<br/><br/>
# Programando 
Abra o seu ambiente de programação e cria uma pasta de projeto 
```
simple-docker-application
|_api
    |_db
        |_Dockerfile
```
<br/><br/>
## **Criação da Imagem e do Container**
Executar o comando: 
```
docker build -t mysql-image -f api/db/Dockerfile .
```


Onde ```-t``` é uma tag, onde damos um nome à imagem.

Onde ```-f``` identifica o arquivo utilizado para gerar a imagem.

Onde ``` .``` ientifica que o contexto da imagem criada é a pasta atual.
<br/><br/>

```
docker image ls
``` 
Lista as imagens do sistema:

```
caiopanes@DET-34523:~/CAIO/Study/simple-docker-application$ docker image ls
REPOSITORY                     TAG       IMAGE ID       CREATED          SIZE
mysql-image                    latest    35737e25851b   19 seconds ago   538MB
```

Para criar o container:
```
docker run -d --rm --name mysql-container mysql-image
```
Onde ```-d``` dá um ```detach``` do terminal do container do nosso terminal.

Onde ```--rm``` remove o container caso ele já tenha sido criado.

Onde ``` --name``` define o nome do container.
<br/><br/>
```
docker ps
```
Lista os containers e seus estados:
```
caiopanes@DET-34523:~/CAIO/Study/simple-docker-application$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                 NAMES
18f23b846816   mysql-image   "docker-entrypoint.s…"   12 seconds ago   Up 10 seconds   3306/tcp, 33060/tcp   mysql-container
```
<br/><br/>
## **Criação do Banco de Dados MySql**
```
simple-docker-application
|_api
    |_db
        |_Dockerfile
        |_script.sql
```
Dentro do arquivo sql:
```
CREATE DATABASE IF NOT EXISTS testedb;
USE testedb;

CREATE TABLE IF NOT EXISTS products (
  id INT(11) AUTO_INCREMENT,
  name VARCHAR(255),
  price DECIMAL(10, 2),
  PRIMARY KEY (id)
);

INSERT INTO products VALUE(0, 'Produto 1', 2500);
INSERT INTO products VALUE(0, 'Produto 2', 900);
```

No terminal executar:
```
docker exec -i mysql-container mysql -uroot -ppassword < api/db/script.sql
```
Onde ```exec``` significa que vamos dar comandos dentro de um container que está rodando.

Onde ```-i``` significa que vamos usar um comando no modo iterativo, ou seja não vai finalizar o processo até que seja concluído.

Onde ```mysql-container``` é o nome do container que estamos acessando.

Onde ```mysql -uroot -ppassword < api/db/scripts.sql``` é o comando que vamos realizar. ```-u``` identifica o usuário root, e ```-p``` identifica a senha.
<br/><br/>

No terminal executar para acessar o container:
```
docker exec -it mysql-container /bin/bash
```
Onde ```-it``` significa que vamos utilizar o tty, ou seja o terminal.

Onde ```/bin/bash``` significa que vamos utilizar o bash.

Este é o terminal do container:
```
caiopanes@DET-34523:~/CAIO/Study/simple-docker-application$ docker exec -it mysql-container /bin/bash
bash-4.4# 
bash-4.4# 
bash-4.4# 
```

Acesse o mysql:
```
bash-4.4# mysql -uroot -ppassword
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```
Acesse o banco de dados com:
```
mysql> use testedb;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> 
```
Selecionar a tabela:
```
mysql> select * from products;
+----+-----------+---------+
| id | name      | price   |
+----+-----------+---------+
|  1 | Produto 1 | 2500.00 |
|  2 | Produto 2 |  900.00 |
+----+-----------+---------+
2 rows in set (0.00 sec)
```
Sair do container:
```
mysql> exit
Bye
bash-4.4# exit
exit
```
<br/><br/>
### Ao dar ```exit``` você perde tudo o que foi executado na linha de comando, para manter o que foi escrito é necessário utilizar **volumes**
<br/><br/>

Para para o container utilize o comando:
```
docker stop mysql-container
```
<br/><br/>


## **Executando o Container com Volume**
```
docker run -d -v $(pwd)/api/db/data:/var/lib/mysql --rm --name mysql-container mysql-image
```

Onde ```-v``` significa que vamos utilizar um volume e ```$(pwd)/api/db/data:/var/lib/mysql``` indica pasta do *container* que está compartilhada com o *host*.

Restore do script sql:
```
docker exec -i mysql-container mysql -uroot -ppassword < api/db/script.sql
```
Isso vai gerar diversos arquivos na pasta ```api/db/data```, que será a estrutura do Banco de Dados.

Caso o container seja parado, o banco de dados materá as informações.

<br/><br/>
## **Instalação da dependências do Node**
Acesse a pasta ```simple-docker-application/api``` e execute:
```
npm init
```
```
caiopanes@DET-34523:~/CAIO/Study/simple-docker-application/api$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help init` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (api) docker-intro-api
version: (1.0.0) 
description: primeira api com docker
entry point: (index.js) 
test command: test
git repository: 
keywords: Docker
author: Caio
license: (ISC) 
About to write to /home/caiopanes/CAIO/Study/simple-docker-application/api/package.json:

{
  "name": "docker-intro-api",
  "version": "1.0.0",
  "description": "primeira api com docker",
  "main": "index.js",
  "scripts": {
    "test": "test"
  },
  "keywords": [
    "Docker"
  ],
  "author": "Caio",
  "license": "ISC"
}


Is this OK? (yes) yes
npm notice 
npm notice New major version of npm available! 8.19.2 -> 9.2.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v9.2.0
npm notice Run npm install -g npm@9.2.0 to update!
npm notice 
```
<br/></br>
Instalar o ```nodemon``` para manter a aplicação node rodando e fazer reload sempre que houver atualizações dos arquivos javascript.
```
npm install --save-dev nodemon
```
E instalar o ```express``` para fazer a rota que vai retornar os produtos, e os drivers do mysql.
```
npm install --save express mysql
```
<br/></br>

No arquivo ```package.json```:

```
simple-docker-application
|_api
    |_db
    |   |_Dockerfile
    |   |_script.sql
    |_node_modules
    |   |_ ...
    |_package-lock.json
    |_package.jason
```
Criar um comando ```start``` que irá iniciar o ```nodemon```:
```
# package.json file:
{
  "name": "docker-intro-api",
  "version": "1.0.0",
  "description": "primeira api com docker",
  "main": "index.js",
  "scripts": {
    "test": "test",
    "start": "nodemon ./src/index"
  },
  "keywords": [
    "Docker"
  ],
  "author": "Caio",
  "license": "ISC",
  "devDependencies": {
    "nodemon": "^2.0.20"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mysql": "^2.18.1"
  }
}
```

<br/></br>
## **API e Conexão com o Banco de Dados**
Criar uma pasta ```src``` e dentro o arquivo ```index.js```:

```
simple-docker-application
|_api
    |_db
    |   |_Dockerfile
    |   |_script.sql
    |_node_modules
    |   |_ ...
    |_package-lock.json
    |_package.jason
    |_src
        |_index.js
```
E escreva:
```
const express = require('express');
const mysql = require('mysql');

const app = express();

const connection = mysql.createConnection({
  host: '172.17.0.2',
  user: 'root',
  password: 'password',
  database: 'testedb'
});

connection.connect();

app.get('/products', function(req, res) {
  connection.query('SELECT * FROM products', function (error, results) {

    if (error) { 
      throw error
    };

    res.send(results.map(item => ({ name: item.name, price: item.price })));
  });
});


app.listen(9001, '0.0.0.0', function() {
  console.log('Listening on port 9001');
})
```
Esse arquivo vai importar as bibliotecas, iniciar uma conexão com o banco de dados, seleciona os produtos e retorna o resultado. 

Uma coisa importante é informar em ```host:``` informar o endereço IP do container, para isso basta executar o comando ```docker inspect mysql-container``` no terminal e procurar o ```"IPAddress":```.

<br/></br>
<br/></br>
# **Criando a Imagem e o Container da Aplicação em si**
Crie um novo Dockerfile.
```
simple-docker-application
|_api
    |_db
    |   |_Dockerfile
    |   |_script.sql
    |_node_modules
    |   |_ ...
    |_package-lock.json
    |_package.jason
    |_src
    |   |_index.js
    |_Dockerfile
```
Nele instale a imagem da versão 10 do node slim, os arquivos do container vão ficar na pasta ```/home/node/app```, é o ```WORKDIR``` (work directory).

Quando o container subir ele deverá executar o comando ```npm start```, então use o ```CMD``` para informar isso ao Dockerfile (verifique o final do arquivo para entender melhor este comando). Esse comando seŕa executado na pasta do ```WORKDIR```.
```
# Dockerfile
FROM node:10-slim
WORKDIR /home/node/app
CMD npm start
```
Então é só criar a imagem como nos passos anteriores:
```
sudo docker build -t node-image -f api/Dockerfile .
```
E rodar a imagem dentro de um conatiner:
```
docker run -d -v $(pwd)/api:/home/node/app -p 9001:9001 --rm --name node-container node-image 
```
Onde ```-p 9001:9001``` informa que a porta 9001 do container poderá ser acessada na porta 9001 do host.

<br/></br>
## **Final**
Por fim basta acessar http://localhost:9001/products no navegador para visualizar.

<br/></br>
<br/></br>
# **Problemas**

https://github.com/ayrtonteshima/docker-introducao/issues/1





<br/></br>
<br/></br>
<br/></br>
<br/></br>
<br/></br>
# **Informações Adicionais**
## **CMD vs ENTRYPOINT**
Resumidamente:

```ENTRYPOINT``` é definido na criação do Dockerfile, pode ser uma função ```sleep``` por exemplo.

```CMD``` é mais como um argumento, pode ser um argumento da função definida pelo ```ENTRYPOINT```, por exemplo ```10``` segundos.

```
docker run example 10
``` 
Somente ```CMD``` de 10 segundos.
<br/></br>
```
docker run --entrypoint sleep2.0 example 10 
```
Muda da função do ```ENTRYPOINT``` para ```sleep2.0```, com um ```CMD``` de ```10``` segundos

Este video possui uma boa explicação sobre o assunto: https://www.youtube.com/watch?v=OYbEWUbmk90

<br/></br>
<br/></br>
<br/></br>
<br/></br>

```
|-----------|
| ENFIM,    |
| É         |
| ISSO.     ||
|-----------|
(\__/) ||
(•ㅅ•) ||
/ 　 づ

CB
```