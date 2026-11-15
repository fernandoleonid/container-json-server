# JSON Server com docker compose

Este projeto inicia uma API REST falsa com [JSON Server](https://github.com), usando o Docker Compose e os dados de `./data/db.json`.

## Pré-requisitos

- Docker instalado e em execução
- Docker Compose (comando `docker compose`)

## Iniciar

Na pasta do projeto, execute para iniciar em segundo plano:

```sh
docker compose up -d
```

Ou, se quiser que o container reinicie automaticamente sempre que você editar o `db.json` (modo desenvolvimento):

```sh
docker compose up --watch
```

> **Nota:** Mesmo usando `--watch`, o servidor apenas reinicia no terminal. **O navegador não atualiza a página automaticamente** — você precisa recarregar a aba (`F5`) ou fazer uma nova requisição para ver os dados atualizados.

O serviço ficará disponível em `http://localhost:3000`. Para conferir se está em execução (quando iniciado com `-d`):

```sh
docker compose ps
docker compose logs -f json-server
```

## Endpoints disponíveis

Cada propriedade de nível superior do `db.json` cria um endpoint. Sem precisar programar regras no backend, a API já responde a consultas simples e avançadas direto pela URL:

- **Listar todos os registros do recurso:**
  - `http://localhost:3000/contatos`
  - `http://localhost:3000/funcionarios`
- **Buscar um registro específico pelo ID:**
  - `http://localhost:3000/contatos/1`
  - `http://localhost:3000/funcionarios/1`
- **Busca por termo (Query String):**
  - `http://localhost:3000/funcionarios?nome_like=silva`
- **Paginação (página e limite de itens):**
  - `http://localhost:3000/contatos?_page=1&_limit=5`

### Métodos HTTP disponíveis

Os endpoints aceitam os seguintes métodos:

- `GET`: lista os registros do endpoint ou consulta um registro pelo ID.
- `POST`: adiciona um novo registro ao endpoint.
- `PUT`: substitui um registro existente pelo ID.
- `DELETE`: remove um registro pelo ID.

As alterações feitas pela API são gravadas no `./data/db.json`, compartilhado entre o host e o container.

## Dados e configuração

Para alterar os dados existentes, edite os registros correspondentes em `./data/db.json`. Para adicionar um endpoint, inclua uma nova propriedade no objeto JSON de nível superior e coloque os registros em uma lista. Por exemplo, acrescente esta propriedade ao objeto existente para criar `/produtos`:

```json
{
  "produtos": [
    {
      "id": 1,
      "nome": "Caneca",
      "preco": 25
    }
  ]
}
```

O endpoint ficará disponível em `http://localhost:3000/produtos`, e o registro poderá ser acessado em `http://localhost:3000/produtos/1`.

Para adicionar outro endpoint, como `/pedidos`, acrescente uma nova propriedade separada por vírgula. Não remova a propriedade `produtos` nem os outros dados que já existem no arquivo. Por exemplo, mantenha as duas propriedades:

```json
{
  "produtos": [
    {
      "id": 1,
      "nome": "Caneca",
      "preco": 25
    }
  ],
  "pedidos": [
    {
      "id": 1,
      "produtoId": 1,
      "quantidade": 2
    }
  ]
}
```

Assim, os endpoints `/produtos` e `/pedidos` ficam disponíveis, além dos endpoints que já estavam no arquivo. Mantenha o `db.json` como JSON válido.

O `docker-compose.yml` mapeia a porta `3000` do host para a porta `3000` do container e monta `./data` em `/data`. Caso tenha iniciado o container sem `--watch`, reinicie o serviço manualmente após editar o arquivo:

```sh
docker compose restart json-server
```

## Parar

Para parar e remover o container e a rede do Compose:

```sh
docker compose down
```

O arquivo `./data/db.json` permanece no projeto.
