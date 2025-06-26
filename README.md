# Laravel Logging Sidecar

Este projeto demonstra como configurar uma aplicação Laravel utilizando Docker e a stack de logs EFK (Elasticsearch, Filebeat e Kibana). O objetivo é fornecer um sidecar de logs que possa ser reaproveitado em outras aplicações.

## Pré-requisitos

- Docker e Docker Compose instalados em sua máquina.
- Acesso à internet para que o Docker baixe as imagens necessárias e para instalar o Laravel com o Composer antes de subir os containers.

## Subindo o projeto

1. Clone este repositório.
2. Execute o comando a seguir para iniciar todos os containers:

```bash
docker compose up -d
```

Certifique-se de que o código do Laravel já está presente na raiz deste repositório. Caso ainda não exista, execute:

```bash
composer create-project laravel/laravel .
```

Após a instalação, você pode subir os containers normalmente.

3. Acesse `http://localhost` no navegador para verificar se a aplicação Laravel está rodando.

4. O Kibana estará disponível em `http://localhost:5601` e o Elasticsearch em `http://localhost:9200`.

## Estrutura dos Containers

- **app**: container PHP-FPM que utiliza o código do Laravel presente na raiz do repositório.
- **nginx**: servidor web que expõe a aplicação na porta 80.
- **mysql**: banco de dados MySQL na porta 3306.
- **elasticsearch**: serviço de busca e armazenamento de logs.
- **kibana**: interface de visualização dos logs enviados ao Elasticsearch.
- **filebeat**: agente responsável por coletar os logs do Nginx e da aplicação Laravel e enviá-los ao Elasticsearch.

Todos os serviços se comunicam através da rede Docker interna `laravel`, e as portas são mapeadas para a sua máquina local para facilitar o desenvolvimento.

## Persistência dos Logs

O Filebeat está configurado para ler os logs de:

- `/var/log/nginx/*.log` (logs de acesso e erro do Nginx)
- `/var/www/storage/logs/*.log` (logs da aplicação Laravel)

Esses logs são enviados para o Elasticsearch e podem ser visualizados no Kibana.

## Personalizações

Caso queira alterar as credenciais do banco de dados ou outros parâmetros, edite o arquivo `docker-compose.yml` antes de subir os containers.

## Finalizando

Para parar e remover os containers, execute:

```bash
docker compose down
```

Pronto! Agora você tem uma aplicação Laravel rodando em Docker com uma stack de logs completa usando EFK.
