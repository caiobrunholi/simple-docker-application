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

```



https://youtu.be/Kzcz-EVKBEQ?t=827
