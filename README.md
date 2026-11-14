# JSON Server em container

Este projeto inicia uma API REST falsa com [JSON Server](https://github.com/typicode/json-server), usando o Docker Compose e os dados de `db.json`.

## Pré-requisitos

- Docker instalado e em execução
- Docker Compose (comando `docker compose`)

## Iniciar

Na pasta do projeto, execute:

```sh
docker compose up -d
```

O serviço ficará disponível em `http://localhost:3000`. Para conferir se está em execução:

```sh
docker compose ps
docker compose logs -f json-server
```

## Endpoints disponíveis

Cada propriedade de nível superior do `db.json` cria um endpoint. Com os dados atuais, estão disponíveis:

- `http://localhost:3000/contatos`
- `http://localhost:3000/funcionarios`

Também é possível acessar um registro pelo ID, por exemplo:

- `http://localhost:3000/contatos/1`
- `http://localhost:3000/funcionarios/1`

### Métodos HTTP disponíveis

Os endpoints aceitam os seguintes métodos:

- `GET`: lista os registros do endpoint ou consulta um registro pelo ID.
- `POST`: adiciona um novo registro ao endpoint.
- `PUT`: substitui um registro existente pelo ID.
- `DELETE`: remove um registro pelo ID.

As alterações feitas pela API são gravadas no `db.json`, compartilhado entre o host e o container.

## Dados e configuração

Para alterar os dados existentes, edite os registros correspondentes em `db.json`. Para adicionar um endpoint, inclua uma nova propriedade no objeto JSON de nível superior e coloque os registros em uma lista. Por exemplo, acrescente esta propriedade ao objeto existente para criar `/produtos`:

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

O `docker-compose.yml` mapeia a porta `3000` do host para a porta `3000` do container e monta `./db.json` em `/data/db.json`. Alterações feitas pela API são gravadas no arquivo local. Após editar o arquivo manualmente, reinicie o serviço para carregar os dados:

```sh
docker compose restart json-server
```

## Parar

Para parar e remover o container e a rede do Compose:

```sh
docker compose down
```

O arquivo `db.json` permanece no projeto.
