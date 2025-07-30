# n8n
About my n8n projects

# Guia para usar o n8n no Docker

Este guia explica como instalar e executar o n8n usando Docker, com Docker Compose e Makefile para facilitar o gerenciamento dos contêineres.

## Pré-requisitos

- Ter o **Docker** instalado na sua máquina. Acesse [docker.com](https://docker.com) para baixar e instalar a versão adequada para seu sistema operacional (Windows, Mac ou Linux).
- No Windows, atenção especial aos requisitos para WSL2 ou HyperV, conforme a documentação oficial do Docker.

## Passos para rodar o n8n com Docker

### 1. Criar a pasta do projeto

No seu computador, crie uma pasta para o projeto, por exemplo:

```bash
mkdir ~/n8n-docker
cd ~/n8n-docker
```

### 2. Criar o arquivo `docker-compose.yml`

Dentro dessa pasta, crie um arquivo chamado `docker-compose.yml` com o seguinte conteúdo básico:

```yaml
version: '3'

services:
  n8n:
    image: n8nio/n8n
    ports:
      - "5678:5678"
    volumes:
      - ./:/home/node/.n8n
    environment:
      - GENERIC_TIMEZONE=America/Sao_Paulo
    restart: unless-stopped
    networks:
      - n8n-network

networks:
  n8n-network:
    driver: bridge
```

**Explicações importantes:**

- **image:** especifica a imagem oficial do n8n no Docker Hub.
- **ports:** mapeia a porta 5678 do contêiner para a porta 5678 local, permitindo acesso via navegador em `http://localhost:5678`.
- **volumes:** sincroniza a pasta atual com a pasta interna do n8n no contêiner, garantindo persistência dos dados.
- **environment:** define variáveis de ambiente, aqui configurando o fuso horário.
- **restart:** garante que o contêiner reinicie automaticamente em caso de falhas ou reinício do sistema.
- **networks:** configura uma rede Docker para comunicação futura entre contêineres, caso queira expandir seu setup.

### 3. (Opcional) Criar um arquivo `Makefile` para facilitar comandos

Para facilitar a execução dos comandos Docker Compose, crie um arquivo chamado `Makefile` com o seguinte conteúdo:

```makefile
deploy:
	docker-compose down -v
	docker-compose up -d
```

Esse comando:

- Para e remove contêineres e volumes antigos.
- Reinicia o serviço em segundo plano (`-d`).

Assim, para levantar o n8n, basta executar no terminal:

```bash
make deploy
```

### 4. Rodar o n8n

No terminal, estando na pasta do projeto, execute:

```bash
make deploy
```

Ou, se não usar o Makefile, pode rodar diretamente:

```bash
docker-compose down -v
docker-compose up -d
```

Isso vai baixar a imagem do n8n (caso ainda não tenha), criar e iniciar o contêiner.

### 5. Acessar o n8n

Abra seu navegador e acesse:

```
http://localhost:5678
```

Você verá a tela de configuração inicial do n8n, onde poderá criar seu usuário, senha e configurar a licença gratuitamente para desbloquear todos os recursos.

### 6. Atualizar o n8n

Para atualizar o n8n para uma versão mais recente, basta parar o contêiner, remover e rodar novamente:

```bash
docker-compose down
docker-compose pull n8n
make deploy
```

Ou, sem Makefile:

```bash
docker-compose down
docker-compose pull n8n
docker-compose up -d
```

Seus workflows e dados ficam preservados porque estão mapeados no volume local.

---

## Considerações finais

- Nunca apague a pasta local onde os dados do n8n estão sendo armazenados (`./` mapeado para `/home/node/.n8n`), pois ali ficam seus projetos e base de dados.
- Para expandir seu ambiente com outros serviços (ex: banco de dados, minio, etc), utilize o Docker Compose para criar múltiplos contêineres e redes internas.
- O Docker garante que o ambiente do seu n8n seja portátil, funcionando em qualquer máquina com Docker instalado, evitando o clássico problema do “na minha máquina funciona”.

---

Assim, com esse guia README, qualquer pessoa com conhecimento básico em Docker poderá rapidamente replicar e rodar o n8n no seu projeto. Se precisar, você pode ainda adicionar links para documentação oficial do n8n e Docker.
