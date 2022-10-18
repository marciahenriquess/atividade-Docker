# WordPress + MYSQL com o Docker Compose   

O Compose é uma ferramenta do Docker usada para executar aplicativos de vários containers. Com um comando, através do arquivo YAML é possível criar todos os serviços de configuração: services, volumes e a rede do aplicativo. 

## 📋 Pré-requisitos
> Ter o Docker e Docker Compose instalados em seu computador.

## 🔧 Instalação
Docker Desktop
> https://docs.docker.com/engine/install/

Docker Compose
> https://docs.docker.com/compose/install/linux/  
> **Observação:** No Docker Desktop o Docker Compose já vem instalado.

## 🚀 Começando  
### 1. Primeiramente, crie um diretório para o seu projeto. 
```
$ mkdir Wordpress 
```
### 2. Mude para o seu diretório.  
```
$ cd ./Wordpress 
```
### 3. Crie o arquivo docker-compose.yml e abra em um editor de texto.
```
$ New-Item docker-compose.yml 
```

## 🛠️ Construção do arquivo Docker Compose  
⚠️Cuidado com a indentação pois o arquivo YAML possui função semântica.  
> Para saber mais sobre linguagem de dados YAML acesse: ahttps://yaml.org/spec/1.2.2/  

### 1. Primeiro, vamos especificar a versão do arquivo .yaml. Vamos usar a versão mais atual com suporte.
```ruby
version: '3.8'
```
### 2. Agora, vamos definir os serviços para a aplicação.
```
Services:
```
#### 2.1 Definindo as configurações do mysql.
```ruby
  mysql:
    image: mysql:8.0.31
    restart: always
    command: mysqld --default_authentication_plugin=mysql_native_password
    volumes:
      - db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: docker
      MYSQL_USER: docker
      MYSQL_PASSWORD: docker
      MYSQL_DATABASE: wordpress
    ports:
      - 3306:3306
    networks:
      - wordpress-network
```
⚠️ Senhas e Usuários
> Senhas e nomes de usuários são dados sensíveis lembre-se de trocá-las no seu arquivo YAML.
* ```mysql:``` -> nome do serviço  
    * ```image: mysql:8.0.31``` -> imagem do mysql que vamos usar (última versão estável)  
    * ```restart: always``` -> será reiniciado sempre em casos de erro.  
    * ```command: mysqld --default_authentication_plugin=mysql_native_password``` -> plugin de autenticação padrão do MYSQL.  
    * ```volumes:```  
      * ```- db_data:/var/lib/mysql``` -> volume e caminho definido para a aplicação.  
    * ```environment:``` -> variáveis de ambiente.  
      * ```MYSQL_ROOT_PASSWORD: docker``` -> senha do usuário root.  
      * ```MYSQL_USER: docker``` -> usuário do mysql, com acesso a database.  
      * ```MYSQL_PASSWORD: docker``` -> senha do usuário mysql.  
      * ```MYSQL_DATABASE: wordpress``` -> nome da database criada.  
    * ```ports:```  
      * ```- 3306:3306``` -> porta que o serviço irá rodar.  
    * ```networks:```  
      * ```- wordpress-network``` -> nome da network que será criada e usada por esse serviço.  
      
#### 2.2 Definindo as configurações do phpMyAdmin.  
> **Observação:** Essa parte é opcional, mas serve para a administração do MySQL pelo navegador.
```ruby
  phpmyadmin:
    depends_on:
      - mysql
    image: phpmyadmin:5.2.0
    restart: always
    ports:
      - 8080:80
    environment:
      PMA_HOST: mysql
      MYSQL_ROOT_PASSWORD: docker
    networks:
      - wordpress-network
```
⚠️ Senhas e Usuários
> Senhas e nomes de usuários são dados sensíveis lembre-se de trocá-las no seu arquivo YAML.
*  ```phpmyadmin:``` -> nome do serviço.  
    * ```depends_on:``` -> este container depende do serviço mysql.  
      * ```- mysql```  
    * ```image: phpmyadmin:5.2.0```-> imagem do phpMyAdmin que vamos usar (última versão estável).  
    * ```restart: always``` -> será reiniciado sempre em casos de erro.  
    * ```ports:``` -> portas que o serviço irá rodar.  
      *  ```- 8080:80``` -> a porta que irá rodar na máquina local (8080) e a porta do container.  
    * ```environment:``` -> variáveis de ambiente.  
      *  ```PMA_HOST: mysql``` -> nome do host do servidor mysql.  
      * ```MYSQL_ROOT_PASSWORD: docker``` -> senha do usuário root. 
    * ```networks:```  
      * ```- wordpress-network``` -> nome da network que será criada e usada por esse serviço.  
      
#### 2.3 Definido as configurações do WordPress.
```ruby
  wordpress:
    image: wordpress:6.0.2
    restart: always
    volumes:
      - wp_data:/var/www/html
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: docker
    ports:
      - 80:80
    networks:
      - wordpress-network
```
⚠️ Senhas e Usuários
> Senhas e nomes de usuários são dados sensíveis lembre-se de trocá-las no seu arquivo YAML.
* ```wordpress:``` -> nome do serviço.
   * ```image: wordpress:6.0.2``` -> imagem do WordPress que vamos usar (última versão estável).
   * ```restart: always``` -> será reiniciado sempre em casos de erro.
   * ```volumes:```
     * ```- wp_data:/var/www/html``` -> volume e caminho definido para a aplicação.
   * ```environment:``` -> variáveis de ambiente.
     * ```WORDPRESS_DB_HOST: mysql```  -> nome do host do WordPress.
     * ```WORDPRESS_DB_NAME: wordpress``` -> nome da database criada para o WordPress.
     * ```WORDPRESS_DB_USER: root``` -> usuário do wordpress. 
     * ```WORDPRESS_DB_PASSWORD: docker``` ->  senha do usuário WordPress.
   * ```ports:``` -> portas que o serviço irá rodar.
     * ```- 80:80``` ->a porta que irá rodar na máquina local (80) e a porta do container.
   * ```networks:```
     * ```- wordpress-network``` -> nome da network que será criada e usada por esse serviço.

### 3. Definindo as configurações de rede.
```ruby
networks:
  wordpress-network:
    driver: bridge
```
* ```networks:```   
  * ```wordpress-network:``` -> nome da network que seja usada pelos serviços.   
    * ```driver: bridge``` -> driver de rede padrão, tipo de rede criada.  
    
### 4. E por fim, criaremos os volumes.
```ruby
volumes:
  db_data:
  wp_data:
```
* ```volumes:```    
  * ```db_data:``` -> volume criado para o MYSQL.   
  * ```wp_data:```  -> volume criado para o WordPress.  


  
## 📃 Arquivo final 
> O arquivo final se encontra neste link: https://github.com/MariaEduardaMaldonado/Atividade-Docker/blob/main/docker-compose.yml  

## 💻 Subindo a aplicação  
⚠️ Comandos
> Para realizar os comandos do Docker Compose é preciso estar dentro do diretório onde o arquivo de configuração (YAML) se encontra.
  
### 1. Abra seu PowerShell como administrador e execute um dos comandos abaixo.  
```
docker-compose up   
```
ou
```
docker-compose up -d
``` 
> A flag '-d' fará com que os containeres rodem em background e você tenha acesso ao seu terminal.  
  
![MicrosoftTeams-image](https://user-images.githubusercontent.com/103605697/196443592-244af770-e4c9-406c-b597-9b04c2dd908e.png)

### 2. Acessar a sua aplicação em seu navegador.  
Abra seu navegador e digite:  
```
http://localhost:80
```
Assim você terá o acesso a configuração inicial do seu WordPress.  

### 3. Configurando seu WordPress  
#### 3.1 Escolha o idioma.   
   
![MicrosoftTeams-image (2)](https://user-images.githubusercontent.com/103605697/196449874-f83da0e8-24ca-417e-a3a6-98f72c146c70.png)    
  
#### 3.2 Preencha os dados abaixo e clique em 'Install WordPress'.  
  
![MicrosoftTeams-image](https://user-images.githubusercontent.com/103605697/196451861-273d8236-16e1-4b86-b07f-5631f53c3bb9.png)   
  
#### 3.3 Faça seu Login.  
  
![MicrosoftTeams-image (3)](https://user-images.githubusercontent.com/103605697/196454072-2af85e98-84a5-4d5d-acb5-a45320ddba50.png)  
  
  
#### 3.4  Prontinho, sua aplicação já está funcionando.     
  
![MicrosoftTeams-image (1)](https://user-images.githubusercontent.com/103605697/196453076-3d600c95-c8ae-4002-9f66-5dbf56bec03e.png)  
   
   
⚠️ Login
> Para fazer login no console do administrador digite:
```
http://localhost:80/wp-admin/
```  
  
## 🔎 Opcional   
> Acessando o PhpMyAdmin.
### 1. Abra seu navegador e digite:  
```
http://localhost:8080
```
Assim você terá o acesso a página de login do seu PhpMyAdmin.  

### 2. Escolha o seu idioma, e entre com o seu usuário e senha do MySQL.  
![MicrosoftTeams-image (4)](https://user-images.githubusercontent.com/103605697/196457775-31263790-7105-4eec-9159-f9997a0bd883.png)   
    
### 3.  Prontinho, agora você terá acesso ao MySQL através do PhpMyAdmin pelo navegador.   
![MicrosoftTeams-image (5)](https://user-images.githubusercontent.com/103605697/196459549-2f561807-5f6e-465b-80f9-4b78f324f314.png)  


## ⚙️ Destruindo
> Comandos de destruição:
```
docker-compose down
```
```
docker-compose down --volumes
```


## 📌 Versões utilizadas 
Docker Desktop: 20.10.17      
Docker Compose: 2.10.2    
Arquivo YAML: 3.8    
Wordpress: 6.0.2     
MySQL: 8.0.31    
PhpMyAdmin: 5.2.0   


## ✒️ Autoras
Maria Eduarda Lopes Maldonado.  
Márcia Cristina Henriques da Cruz.
