<div align="center">
<h1>Ambiente de desenvolvimento local LEMP com Docker</h1>
  <p></p>
  <p align="center">
    <a href="#introdução-"><b>O que é isso?</b></a>
    —  
    <a href="#iniciando-"><b>Iniciando</b></a>
    —  
    <a href="#comandos-úteis-"><b>Comandos úteis</b></a>
    —  
    <a href="#leitura-adicional-"><b>Leitura adicional</b></a>
  </p>
  <br />
</div>

__Um ambiente de desenvolvimento local diário para desenvolvedores PHP__ que procuram um início rápido. Espero que este projeto também possa facilitar e agilizar o seu dia.

---

## Introdução 👋

Este é um conjunto de imagens do Docker que cria um ambiente LEMP (Linux, Nginx, MySQL e PHP) para desenvolvimento local utilizando volumes para realizar o compartilhando da sua raiz entre host e container. 

A estrutura de pasta pronta para realizar o inicio de um projeto a partir da raiz utilizando a pasta public para carregar a sua index.php.

Veja a estrutura dos diretório:
```
.
├── .docker
│   ├── config
│   │   ├── docker-php-ext-xdebug.ini
│   │   ├── fpm-pool.conf
│   │   ├── nginx.conf
│   │   ├── php.ini
│   │   └── supervisord.conf
│   └── Dockerfile
├── docker-compose.yml
├── .gitignore
├── public
│   └── index.php
└── README.md
```

Todas as configurações do ambiente ficam dentro da pasta ```./.docker``` incluindo arquivo Dockerfile que utiliza como base a imagem alpine para instalar php e suas dependências.

O docker-compose esta configurado para subir tres containers:

|CONTAINER    |DESCRIÇÃO                               |PORTAS                                    |
|-------------|----------------------------------------|-----------------------------------------|
|app_php      |Execução do php e suas dependências     |0.0.0.0:8080->8080/tcp, :::8080->8080/tcp|
|phpmyadmin   |Utilitário para administração do banco  |0.0.0.0:8081->80/tcp, :::8081->80/tcp    |
|db_mysql8    |Banco de dados mysql                    |3306/tcp, 33060/tcp                      |

*Comportamento:*
- app_php
  - Os arquivo/diretório da raiz deste projeto são espelhados no container através do diretório ```/var/www```
  - Veja [aqui](#alterar-privilegio-do-container-app-) como ajustar o permissionamento.   
- db_mysql8
  - Os dados deste container NÃO estão sendo persistido dentro host.
  - Veja [aqui](#leitura-adicional-) alguns links uteis que possa te ajudar.  

Inclui todas as dependências necessárias para o desenvolvimento diário de PHP com ferramentas comuns:

*Predefinição:*

- Nginx (mais recente)*
- PHP 8.0
    - Composer (mais recente)*
    - Node.js e NPM (mais recente)*
    - Yarn (mais recente)*
    - xDebug (mais recente)*
- Mysql 8

*Opcional:*

- phpmyadmin (mais recente)*

---

## Prerequisites ⚠️

Primeiro, você precisará instalar o docker e docker-compose ;).

---

## Iniciando 🚀

```bash
# Clonar o repositório
git clone https://github.com/marceloolima/docker-dev-php8.git && cd docker-dev-php8

# Subir ambiente
docker-compose -f "docker-compose.yml" up -d --build

# Destruir ambiente
docker-compose -f "docker-compose.yml" down 
```

---

## Alterar privilegio do container app 😀
Para trabalhar com o compartilhamento do volume entre host e container um usuário é criado dentro do container *app_php* com o mesmo UID do host. 

```bash
  # Atualização do UID
  #  - UID 1000 foi definido nesta imagem com padrão  
  #  - UID é definido no arquivo docker-composer.yml
  sed -i "s/uid: 1000/uid: $UID/" docker-compose.yml

```

---

## Comandos úteis 🔥

O Docker Engine deve estar em execução e os comandos devem ser executados na raiz deste repositório.

| Comando | Descrição |
|---|---|
| `docker-compose start` | Iniciar todos os contêineres |
| `docker-compose stop`  | Parar todos os contêineres (mantém todas as alterações de configuração que você fez nos contêineres) |
| `docker-compose up -d --build --no-cache` | Recrie todos os contêineres do zero |
| `docker-compose down`  | Derrube todos os contêineres |
| `docker-compose run --rm app composer install` | roda o comando composer install dentro da raiz do projeto |
| `docker-compose logs app` | Mostrar todos os logs do container app |
| `docker-compose ps` | Mostrar quais contêineres estão em execução |

## Leitura adicional 📚
- [DockerHub](https://hub.docker.com/)
    - [Mysql](https://hub.docker.com/_/mysql)
    - [Alpine](https://hub.docker.com/_/alpine)
    - [phpMyAdmin](https://hub.docker.com/_/phpmyadmin)
- [Docker](https://docs.docker.com/)
- [Nginx](http://nginx.org/en/docs/)
- [Php-fpm](https://www.php.net/manual/en/install.fpm.configuration.php)
- [Supervisord](http://supervisord.org/)
- [Packages Alpine](https://pkgs.alpinelinux.org/packages)
- [xDebug](https://xdebug.org/docs/all_settings)


