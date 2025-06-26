# Stack de Logs com Filebeat, Elasticsearch e Kibana

Este projeto demonstra como configurar um sidecar de logs utilizando Filebeat para enviar arquivos JSON para o Elasticsearch e visualizá-los no Kibana.

## Requisitos

- [Docker](https://www.docker.com/) e [Docker Compose](https://docs.docker.com/compose/) instalados.

### Instalação do Docker

1. Baixe e instale o Docker conforme a documentação oficial: <https://docs.docker.com/get-docker/>
2. Instale o Docker Compose: <https://docs.docker.com/compose/install/>

## Subindo o ambiente

Com o Docker instalado, clone este repositório e execute:

```bash
docker-compose up
```

Isso iniciará três containers:

- **elasticsearch** – Armazena e indexa os logs. Disponível em `http://localhost:9200`.
- **kibana** – Interface web para visualizar os dados do Elasticsearch. Disponível em `http://localhost:5601`.
- **filebeat** – Responsável por coletar arquivos de log em `/app/logs` e enviá-los para o Elasticsearch.

Todos os serviços compartilham a mesma rede interna e podem se comunicar entre si.

## Adicionando logs de teste

Coloque arquivos `.log` no diretório `./logs`. Por exemplo, já existe um arquivo `./logs/test.log` com o seguinte conteúdo:

```json
{"timestamp": "2025-06-26T13:00:00Z", "level": "INFO", "message": "Log de teste", "service": "laravel-app"}
```

O Filebeat irá ler automaticamente os arquivos presentes nesse diretório e enviá-los para o Elasticsearch, utilizando o índice `logs-sidecar-%{+yyyy.MM.dd}`.

## Acessando as aplicações

- Acesse o Kibana em: [http://localhost:5601](http://localhost:5601)
- Acesse o Elasticsearch em: [http://localhost:9200](http://localhost:9200)

Agora você pode explorar os logs coletados e visualizar dashboards pelo Kibana.

## Autenticação e acesso seguro

Com a stack subida, o Elasticsearch inicia com segurança ativada e define a senha do usuário `elastic` como `changeme` (variável `ELASTIC_PASSWORD`).

Caso prefira gerar um token de acesso, execute:

```bash
docker-compose exec elasticsearch bin/elasticsearch-create-enrollment-token -s kibana
```

Kibana e Filebeat já estão configurados para se autenticar usando `elastic` / `changeme`.

### Acessando o Kibana

Acesse [http://localhost:5601](http://localhost:5601) no navegador e faça login com `elastic` / `changeme`.

### Testando a conexão com o Elasticsearch

Execute o comando abaixo para verificar o acesso usando autenticação:

```bash
curl -u elastic:changeme http://localhost:9200
```
