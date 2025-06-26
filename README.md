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

### ⚙️ Geração do token para Kibana

1. Após subir o container do Elasticsearch com:
   ```bash
   docker compose up -d elasticsearch
   ```
   Acesse o container:
   ```bash
   docker exec -it elasticsearch bash
   ```
   Gere o token de serviço do Kibana:
   ```bash
   elasticsearch-create-enrollment-token --scope kibana
   ```
   Copie o token retornado e defina em um arquivo `.env`:
   ```env
   KIBANA_SERVICE_TOKEN=eyJ2ZXIiOiI... (cole aqui)
   ```
   Suba os serviços restantes:
   ```bash
   docker compose up -d
   ```
   Acesse o Kibana em: http://localhost:5601

Filebeat continua autenticando com `elastic` / `changeme`.

### Acessando o Kibana

Acesse [http://localhost:5601](http://localhost:5601) no navegador e faça login com `elastic` / `changeme`.

### Testando a conexão com o Elasticsearch

Execute o comando abaixo para verificar o acesso usando autenticação:

```bash
curl -u elastic:changeme http://localhost:9200
```

## 🔐 Configuração segura do Kibana com Elasticsearch

Para rodar o Kibana com autenticação correta no Elasticsearch (sem usar o usuário `elastic`, que é bloqueado), siga os passos abaixo:

### 1. Suba o Elasticsearch

```bash
docker compose up -d elasticsearch
```

### 2. Gere o token de service account para o Kibana

Acesse o container do Elasticsearch:

```bash
docker exec -it elasticsearch bash
```

Dentro do container, execute:

```bash
elasticsearch-service-tokens create kibana kibana-token
```

O comando irá retornar algo como:

```bash
kibana/kibana-token: AAEAAWVsYXN0aWMva2liYW5hL2tpYmFuYS10b2tlbgAAAAC...
```

Copie apenas o valor após os dois pontos (:), que é o token JWT.

### 3. Crie um arquivo .env no diretório raiz do projeto com o seguinte conteúdo:

```env
KIBANA_SERVICE_TOKEN=AAEAAWVsYXN0aWMva2liYW5hL2tpYmFuYS10b2tlbgAAAAC...
```

### 4. Garanta que o docker-compose.yml do Kibana esteja configurado assim:

```yaml
environment:
  - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
  - ELASTICSEARCH_SERVICEACCOUNTTOKEN=${KIBANA_SERVICE_TOKEN}
```

### 5. Suba os demais serviços

```bash
docker compose up -d
```

### 6. Acesse o Kibana

Abra o navegador e vá para:

```arduino
http://localhost:5601
```
