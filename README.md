# Nossas Lojas V3

API de exercício em **Node.js, TypeScript, Express e MySQL** para gerenciar lojas físicas de um e-commerce e seus clientes. Esta versão usa entidades e relações com TypeORM.

[![TypeScript](https://img.shields.io/badge/TypeScript-project-3178C6)](https://www.typescriptlang.org/docs/)
[![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE)

## 📋 Table of contents

- [✨ Features](#features)
- [🧪 Technologies](#technologies)
- [🛠️ Environment setup](#environment-setup)
- [🚀 Getting started](#getting-started)
- [🐋 Getting started with Docker](#getting-started-with-docker)
- [🔌 API usage](#api-usage)
- [🗂️ Project structure](#project-structure)
- [✅ Testing and quality](#testing-and-quality)
- [🔎 Troubleshooting](#troubleshooting)
- [🤝 Contributing](#contributing)
- [📜 License](#license)

<a id="features"></a>

## ✨ Features

- Cadastro, atualização, exclusão e consulta de lojas físicas e clientes.
- Associação de clientes às lojas por TypeORM.
- Consultas por estado/cidades, nome, CPF, RG, gênero e local de trabalho.
- Interface Swagger em `/api-docs`.

Atividade baseada na [trilha de microsserviços Node.js](https://github.com/myreli/c_trilhas/blob/master/microservices_node/README.md) e no [desafio de clientes e lojas](https://github.com/myreli/c_trilhas/blob/master/microservices_node/desafio2/README.md).

<a id="technologies"></a>

## 🧪 Technologies

| Tecnologia | Versão declarada | Finalidade |
| --- | --- | --- |
| [Node.js](https://nodejs.org/) | Não definida no projeto | Runtime |
| [TypeScript](https://www.typescriptlang.org/docs/) | `^2.8.1` | Compilação para `js/` |
| [Express](https://expressjs.com/) | `^4.16.3` | Rotas HTTP |
| [TypeORM](https://typeorm.io/) | `^0.2.0` | Conexão MySQL |
| [MySQL driver](https://github.com/mysqljs/mysql) | `^2.15.0` | Acesso ao banco |
| [express-validator](https://express-validator.github.io/docs/) | `^5.3.1` | Validação das requisições |

As versões são faixas do manifesto, não versões resolvidas. Não há lockfile nem versão de Node em `engines`. Node **14.16.0** foi escolhido para a tentativa de reprodução do projeto legado; essa escolha não garante compatibilidade de dependências transitivas atuais. Atualizações de runtime/dependências exigem validação separada.

<a id="environment-setup"></a>

## 🛠️ Environment setup

Use Git, [nvm](https://github.com/nvm-sh/nvm) e Node.js **14.16.0**. Os comandos abaixo usam npm, fornecido com o Node.js. Não há lockfile versionado; use `npm install`, e não `npm ci`. A resolução das faixas de versão pode variar entre instalações.

```bash
nvm install 14.16.0
nvm use 14.16.0
git clone https://github.com/paulodaluz/NossasLojasV3.git
cd NossasLojasV3
npm install
```

### MySQL

Antes da execução, disponibilize uma instância MySQL própria e uma base vazia. Ajuste `ormconfig.json` com os dados locais:

```json
{
  "type": "mysql",
  "host": "127.0.0.1",
  "port": 3306,
  "username": "usuario_exemplo",
  "password": "senha_ficticia",
  "database": "ecommerce",
  "synchronize": true,
  "entities": ["js/entity/*.js"],
  "subscribers": ["js/subscriber/*.js"],
  "migrations": ["js/migration/*.js"],
  "cli": {
    "entitiesDir": "js/entity",
    "migrationsDir": "js/migration",
    "subscribersDir": "js/subscriber"
  }
}
```

Os valores acima são exemplos; não publique credenciais. `synchronize: true` permite ao TypeORM alterar o esquema: use uma base de desenvolvimento. O projeto não lê esse arquivo a partir de variáveis `.env`. O servidor só abre a porta após `createConnection()` concluir. Não há `prepare` ou hooks de Git configurados.

<a id="getting-started"></a>

## 🚀 Getting started

```bash
./node_modules/.bin/tsc
npm start
```

`npm start` executa `tsc -w` e `nodemon js/index.js` em paralelo. A porta fixa é **3000** e não há variável `PORT`. Com o banco acessível, abra `http://localhost:3000/api-docs`; `/` redireciona para a documentação.

```bash
curl http://localhost:3000/listaLojas
```

Esse comando depende da conexão e das tabelas do MySQL; não há rota de saúde independente do banco.

<a id="getting-started-with-docker"></a>

## 🐋 Getting started with Docker

O arquivo existente é [nodes.dockerfile](nodes.dockerfile), com contexto na raiz, imagem `node:latest`, diretório `/var/www`, `npm install`, `npm start` e porta exposta `3000`. Não há Compose, volumes ou serviço MySQL definidos.

```bash
docker build -f nodes.dockerfile -t nossas-lojas-v3 .
docker run --rm --name nossas-lojas-v3 -p 3000:3000 nossas-lojas-v3
```

Configure um host MySQL alcançável pelo container em `ormconfig.json` antes do build: `localhost` dentro dele não é o banco do host. O `COPY .` inclui a configuração; não distribua imagens contendo credenciais. O Dockerfile desativa a validação TLS do npm e não fixa a versão do Node, divergindo da reprodução local. A revisão desses pontos é uma pendência; nenhum arquivo Docker foi modificado. **Comandos documentados por inspeção, sem validação por execução nesta tarefa.**

<a id="api-usage"></a>

## 🔌 API usage

Não há middleware de autenticação nas rotas. Caminhos abaixo correspondem a `src/rotas/`, que prevalece sobre exemplos antigos.

| Método | Caminho | Função |
| --- | --- | --- |
| POST | `/criaLoja` | Cadastrar loja |
| PUT | `/editaLoja/:id` | Atualizar loja |
| DELETE | `/deletaLoja/:id` | Excluir loja |
| GET | `/buscaPorIdL/:id` | Consultar loja |
| GET | `/buscaPorEstado/:estado` | Filtrar lojas |
| POST | `/buscaPorCidades/:estado` | Filtrar por cidades no corpo |
| GET | `/listaLojas` | Listar lojas |
| POST | `/criaCliente` | Cadastrar cliente |
| PUT | `/editaCliente/:id` | Atualizar cliente |
| DELETE | `/deletaCliente/:id` | Excluir cliente |
| GET | `/buscaPorIdC/:id` | Consultar cliente |
| POST | `/buscaPorNome` | Filtrar nomes no corpo |
| GET | `/buscaPorCPF/:cpf` | Filtrar CPF |
| GET | `/buscaPorRG/:rg` | Filtrar RG |
| GET | `/buscaPorGenero/:genero` | Filtrar gênero |
| POST | `/buscaPorTrabalho` | Filtrar local de trabalho |
| GET | `/listaClientes` | Listar clientes |

Exemplo com dados fictícios em uma base de desenvolvimento:

```bash
curl -X POST http://localhost:3000/criaLoja \
  -H 'Content-Type: application/json' \
  -d '{"nome_loja":"Loja Exemplo","endereco":"Rua Exemplo, 1","celular":"00000000000","cnpj":"00000000000000","horarioDeTrabalho":"09h às 18h","cidade":"Cidade Exemplo","estado":"EX"}'
```

A criação de cliente recebe `nome`, `cpf`, `rg`, `genero`, `celular`, `endereco`, `localDeTrabalho` e `lojaId`; utilize o ID de uma loja existente. Consulte as validações em `src/models/`.

A definição servida é [Documentacao/swagger.json](Documentacao/swagger.json). Também existem [swaggerCliente.json](Documentacao/swaggerCliente.json), [swaggerLoja.json](Documentacao/swaggerLoja.json) e [links históricos do Postman](Links%20do%20Postman.txt); confira caminhos e métodos no código antes de usá-los.

<a id="project-structure"></a>

## 🗂️ Project structure

| Caminho | Responsabilidade |
| --- | --- |
| `src/index.ts` | Conexão, middleware, Swagger e servidor |
| `src/rotas/` | Registro de rotas de lojas e clientes |
| `src/controller/` | Operações HTTP e persistência |
| `src/entity/` | Entidades `ListaLojas` e `Clientes` |
| `src/models/` | Validações e mensagens |
| `src/services/` | Serviço de clientes |
| `Documentacao/` | Definições Swagger |
| `ormconfig.json` | MySQL e descoberta de entidades compiladas |

<a id="testing-and-quality"></a>

## ✅ Testing and quality

Não existem scripts de testes, lint ou build. Use `./node_modules/.bin/tsc` para verificar a compilação; `npm run watch` acompanha alterações e `npm run server` inicia o JavaScript gerado. O watcher pode permanecer aberto mesmo quando há erros de compilação.

### Validation recorded

Em 11/11/2026, a instalação com Node 14.16.0/npm 6.14.11 concluiu. A compilação retornou código 2 por incompatibilidades nos tipos resolvidos em `@types/express-serve-static-core`. O JavaScript emitido foi iniciado, mas a conexão MySQL em `127.0.0.1:3306` retornou `ECONNREFUSED`; o servidor HTTP não abriu. As operações da API não foram verificadas. Nenhum serviço MySQL foi instalado.

<a id="troubleshooting"></a>

## 🔎 Troubleshooting

- Servidor não abre a porta: a conexão MySQL precisa concluir primeiro.
- Erros em arquivos de tipos: TypeScript 2.x e dependências sem lockfile podem divergir; a correção de versões fica para revisão.
- Rota antiga retorna `404`: use `/buscaPorIdL/:id`, `/buscaPorEstado/:estado` e método POST em `/buscaPorCidades/:estado`.
- Em container, banco inacessível em `localhost`: configure o endereço correto para a rede utilizada; o repositório não fornece MySQL via Docker.

<a id="contributing"></a>

## 🤝 Contributing

Crie uma branch a partir de `master`, valide compilação e fluxos em MySQL de desenvolvimento e abra um PR com o resultado. Preserve dados fictícios nos exemplos e mantenha Swagger alinhado às rotas.

<a id="license"></a>

## 📜 License

Distribuído sob a licença **MIT**, preservada em [LICENSE](LICENSE). O manifesto mantém referências ao exemplo TypeORM de origem.

---

<p align="center">Made with 💜 by <a href="https://github.com/paulodaluz">Paulo da Luz</a></p>
