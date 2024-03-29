# Documentação Show do Devops 1.0
https://youtu.be/NlMCSnaAZm0

obs : é necessário ter o docker instalado em seu Linux (apenas linux)

### Instalação do Docker

- Usuários que tem o seu sistema baseado em Debian podem usar o apt-get ou o aptitude:

sudo apt-get update

sudo apt-get install docker.io

- Usuários que tem o seu sistema baseado em RedHat podem usar o yum:

yum install docker

### Instalação do Docker compose

yum install docker-compose


# Opção 1 - Subindo através de script Shell

Veja os passos no link abaixo a partir do passo C:
https://youtu.be/NlMCSnaAZm0

A - baixar este repositório em sua máquina
B - descompactar a pasta Enquete.tar.gz no terminal com o comando: tar -xvzf Enquete.tar.gz  
C - dentro da pasta Enquete existe dois arquivos, para subir os containers execute:

- subindo os containers dockers
./Enquete_start.sh

- abra o chome e abra o link abaixo que ja vai estar funcionando:
localhost:800  

- quando precisar dar stop
./Enquete_stop.sh




# Opção 2 - Usando Docker Compose com Pipeline Jenkinsfile ( A opção mais facil para quem já esta familiarizado )

### Atenção: se algum serviço usa as portas (800,3000,3306) precisam estar down.

### A) Configure seu pipeline no Jenkins com o Jenkinsfile abaixo

https://github.com/diellyr/Jenkinsfiles_Examples/blob/master/Jenkinsfile_docker-compose_Enquete_devops


- ### dicas para dar permissão de sudo para o Jenkins e Docker

- dica1: usuário Jenkins com permissão sudo

(incluir em /etc/sudoers)
jenkins ALL=(ALL) NOPASSWD: ALL

- dica2: permissão para o usuario jenkins com docker

sudo usermod -a -G docker jenkins


### B) testando a aplicação

(abra um navegador , apenas no chrome funcionou corretamente )

(aqui sua aplicação já deve estar funcionando)
http://localhost:800/



(no final tem uma seção de troubleshooting, apenas se houver algum erro)


### parando e subindo os conteineres

- parar o banco

docker stop mariadb, 
docker start mariadb

- parar o nodejs

docker stop node
docker start node

- parar o apache

docker stop http
docker start http


#Fim


------------------------------------------------------------------------------------------


# Opção 3 - Usando com Docker compose

### A) - baixando os dados da aplicação 

descompacte o arquivo Enquete1.0.tar.gz dentro de /root
    
     
### B) inicie o docker-compose

$ cd /root/Enquete/compose/

$ docker-compose up

### C) testando a aplicação

(abra um navegador , no chrome funcionou o som no inicio mas é instável, o firefox é o mais indicado )

(aqui sua aplicação já deve estar funcionando)
http://localhost:800/



(no final tem uma seção de troubleshooting, apenas se houver algum erro)


### parando e subindo os conteineres

- parar o banco

docker stop mariadb, 
docker start mariadb

- parar o nodejs

docker stop node
docker start node

- parar o apache

docker stop http
docker start http



# Fim



------------------------------------------------------------------------------------------








# Opção 4 - subindo cada container manualmente

## Baixar os conteiners e a aplicação

### A) - baixando os dados da aplicação 

descompacte o arquivo enquete.tar.gz dentro de /root

### B) - caso queira primeiro baixar os containers ( mas pode pular Direto para o passo C )

docker pull diellyr/enquete.mariadb
docker pull diellyr/enquete.nodejs
docker pull diellyr/enquete.http


### C) - iniciando os dockers

### - o Primeiro start pode ser feito conforme abaixo

- start mariadb

docker run -it --name mariadb -v /root/Enquete/mariadb:/var/lib/mysql -p 3306:3306 -d diellyr/enquete.mariadb

- start nodejs

docker run -it --name node -v /root/Enquete/nodejs:/root -p 3000:3000 -d diellyr/enquete.nodejs

- start apache

docker run -it --name httpd -v /root/Enquete/apache:/app -p 800:80 -d diellyr/enquete.http

obs1: depois pode usar apenas os comandos mais abaixo "parando e subindo os conteiner" para start dos conteineres, "caso precise matar o conteiners e iniciar tudo novamente use os comandos acima ;) "


Pronto! 

agora é só acessar o endereço abaixo:

### Acesso a Enquete
http://localhost:800



### parando e subindo os conteineres

- parar o banco

docker stop mariadb, 
docker start mariadb

- parar o nodejs

docker stop node
docker start node

- parar o apache

docker stop http
docker start http




..........................................................................

### parando e subindo os conteineres

- para o banco
docker stop mariadb, 
docker start mariadb

- para o nodejs
docker stop node
docker start node

- para o apache
docker stop http
docker start http


-------------------------------------------------------------------------------------------


# Opção 5 - Usando com Docker compose ( Arquivo antigo ) (somente se houver problemas na Opção 1 no começo desta página) 

### A) - baixando os dados da aplicação 

descompacte o arquivo Enquete.tar.gz dentro de /root

### B) - altere o apontamento do nodejs para o mariadb conforme abaixo 

$ vim /root/Enquete/nodejs/select.js
//    host     : '172.20.0.2',
host     : 'mariadb',

### C) - crie um novo diretorio e adicione o arquivo abaixo ( faça um clone deste repositório para ter a indentação)

$ mkdir /root/Enquete/compose/

$ vim docker-compose.yml

version: '3'
services: 
  db:
    image: diellyr/enquete.mariadb
    restart: always
    hostname: mariadb
    container_name: mariadb
    environment:
      - MYSQL_USER=root
      - MYSQL_ROOT_PASSWORD=senhadoroot 
    volumes:
      - ../mariadb:/var/lib/mysql
    ports: 
      - 3306:3306
  backend:
    image: diellyr/enquete.nodejs
    hostname: node
    container_name: node
    volumes: 
      - ../nodejs:/root
    ports:
      - 3000:3000
  frontend: 
    image: diellyr/enquete.http
    hostname: http
    container_name: http
    volumes: 
      - ../apache:/app
    ports: 
      - 800:80
      
      
### D) inicie o docker-compose

$ cd /root/Enquete/compose/

$ docker-compose up

### E) testando a aplicação

(abra um navegador , apenas no chrome funcionou o som de inicio, no firefox funciona os demais sons )

(aqui sua aplicação já deve estar funcionando)
http://localhost:800/


(no final tem uma seção de troubleshooting)


### parando e subindo os conteineres

- parar o banco

docker stop mariadb, 
docker start mariadb

- parar o nodejs

docker stop node
docker start node

- parar o apache

docker stop http
docker start http



--------------------------------------------------------------------------------------------

### troubleshouting

- verificando se o nodejs está funcionando (caso precise testar a chamada direto ao nodejs)

http://localhost:3000/perguntas/1   

obs: o numero no final é a sequencia das perguntas

-  subindo o nodejs manualmente

obs2: caso, apenas caso precise iniciar o npm do nodejs manualmente, será  necessário entrar no conteiner conforme comando abaixo:

- comando 1
docker exec -it node bash

- comando 2
nohup npm start &

- comando 3 ( caso queira validar)
tail -f nohup.out 

(vai aparecer deste jeito) significa que o node foi iniciado
> nodemysql@1.0.0 start /root
> nodejs select.js
