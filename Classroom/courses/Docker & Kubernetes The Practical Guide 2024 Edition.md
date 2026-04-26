# Docker & Kubernetes: The Practical Guide 2024

## Section 1: Getting Started

**Docker** é uma tecnologia de containers — uma ferramenta para criar e gerenciar containers.
O mesmo container sempre produz o mesmo comportamento de execução, independente de onde ou por quem for executado.

### Conceitos Fundamentais

#### Images vs Containers (`#18`)

**Image** e **Container** são os dois conceitos centrais do Docker, e entender a diferença entre eles é fundamental para
trabalhar com a tecnologia.

##### Image

Uma image é um **pacote estático, imutável e somente leitura** que contém tudo o que a aplicação precisa para rodar:
código fonte, runtime (ex: Node.js, Python), bibliotecas, variáveis de ambiente e configurações do sistema de arquivos.

- É criada a partir de um `Dockerfile` com o comando `docker build`
- Uma vez criada, **não muda** — ela é como uma fotografia do estado da aplicação
- Pode ser armazenada e compartilhada via Docker Hub ou outros registries
- A mesma image pode gerar **múltiplos containers** rodando simultaneamente
- É composta por **camadas (layers)**: cada instrução do `Dockerfile` gera uma camada, e o Docker as reutiliza em cache
  para builds mais rápidos

##### Container

Um container é uma **instância em execução de uma image** — é a image "ganhando vida". Ele é um processo isolado rodando
no sistema operacional do host, com seu próprio sistema de arquivos, rede e recursos.

- É criado a partir de uma image com o comando `docker run`
- Possui uma **camada de escrita própria** (read/write layer) por cima das camadas read-only da image
- Dados gravados dentro do container existem apenas enquanto ele existir (a menos que haja um volume)
- Pode ser **parado, reiniciado e removido** sem afetar a image original
- Múltiplos containers podem rodar a partir da **mesma image** ao mesmo tempo, completamente isolados entre si

##### Analogia

Pense na image como a **receita de um bolo** e no container como o **bolo assado**. A receita não muda (imutável), mas
você pode assar quantos bolos quiser a partir dela. Cada bolo é independente — se você queimar um, a receita continua
intacta e os outros bolos não são afetados.

|          | Image                                  | Container                               |
|----------|----------------------------------------|-----------------------------------------|
| O que é  | Template/Blueprint estático            | Instância em execução da image          |
| Estado   | Imutável (read-only)                   | Mutável durante execução (read/write)   |
| Conteúdo | Código + ferramentas/runtimes          | Processo isolado rodando no host        |
| Criação  | `docker build`                         | `docker run`                            |
| Ciclo    | Existe até ser removida (`docker rmi`) | Existe enquanto roda ou até `docker rm` |
| Relação  | Uma image → múltiplos containers       | Cada container = uma instância da image |

> Rodamos containers baseados em images. A image nunca é alterada pelo container — cada container tem sua própria camada
> de escrita isolada.

---

### Comandos Essenciais

#### `docker build`

**O que é:** Compila o `Dockerfile` e gera uma image Docker local.

**Para que serve:** Toda vez que você altera o código ou o Dockerfile, precisa rebuildar a image para que as mudanças
entrem em efeito.

```bash
# Forma básica: gera uma image sem nome (identificada só por ID)
docker build .

# Forma recomendada: gera a image com nome e tag
docker build -t minha-app:1.0 .
docker build -t minha-app:latest .
```

> O `.` indica que o `Dockerfile` está no diretório atual.

---

#### `docker images`

**O que é:** Lista todas as images Docker disponíveis localmente.

**Para que serve:** Ver quais images você já baixou ou construiu, seus tamanhos, tags e IDs.

```bash
docker images
# REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
# minha-app     latest    3f4a2b1c9d8e   2 hours ago    142MB
# node          18        a1b2c3d4e5f6   3 days ago     991MB
```

---

#### `docker rmi`

**O que é:** Remove uma ou mais images locais.

**Para que serve:** Liberar espaço em disco removendo images que não são mais necessárias. O container que usa a image
deve estar parado antes de removê-la.

```bash
docker rmi 3f4a2b1c9d8e           # Remove pelo ID
docker rmi minha-app:latest       # Remove pelo nome:tag
docker rmi img1 img2 img3         # Remove múltiplas images de uma vez
```

---

#### `docker images prune`

**O que é:** Remove automaticamente todas as images sem tag ("dangling images").

**Para que serve:** Limpar images intermediárias que ficam acumuladas após vários `docker build`, sem precisar remover
manualmente uma por uma.

```bash
docker images prune          # Remove apenas dangling images
docker images prune -a       # Remove TODAS as images não usadas por nenhum container
```

---

#### `docker run`

**O que é:** Cria e inicia um novo container a partir de uma image.

**Para que serve:** É o comando principal para executar sua aplicação. Cada `docker run` cria um container novo.

```bash
# Forma básica
docker run node:18

# Com mapeamento de porta: host:container
docker run -p 3000:80 minha-app

# Modo detached (roda em background, libera o terminal)
docker run -d -p 3000:80 minha-app

# Com nome personalizado e auto-remoção ao parar
docker run -d -p 3000:80 --rm --name meu-container minha-app

# Modo interativo (para aplicações que precisam de terminal, como React)
docker run -it minha-app
```

---

#### `docker ps`

**O que é:** Lista os containers Docker em execução.

**Para que serve:** Monitorar quais containers estão ativos, ver seus IDs, nomes, portas e status.

```bash
docker ps           # Mostra apenas containers em execução
docker ps -a        # Mostra TODOS os containers (inclusive os parados)

# CONTAINER ID   IMAGE        COMMAND      STATUS         PORTS                  NAMES
# a1b2c3d4e5f6   minha-app    "node app"   Up 5 minutes   0.0.0.0:3000->80/tcp   meu-container
```

---

#### `docker stop`

**O que é:** Para um container em execução de forma segura (envia SIGTERM).

**Para que serve:** Encerrar um container sem destruí-lo. O container para mas seus dados internos permanecem (a menos
que tenha usado `--rm`).

```bash
docker stop meu-container         # Para pelo nome
docker stop a1b2c3d4e5f6          # Para pelo ID
docker stop container1 container2  # Para múltiplos de uma vez
```

---

#### `docker container prune`

**O que é:** Remove todos os containers que estão parados.

**Para que serve:** Limpeza rápida do ambiente, liberando espaço sem precisar remover container por container.

```bash
docker container prune
# WARNING! This will remove all stopped containers.
# Are you sure you want to continue? [y/N]
```

---

## Section 3: Managing Data & Working with Volumes

### Naming & Tagging (`#36`)

**O que são nomes e tags:** Nome identifica a image (ex: `minha-app`), tag identifica a versão (ex: `1.0`, `dev`,
`latest`). Containers também podem ter nomes para facilitar referência nos comandos.

**Para que servem:** Sem nome/tag, a image fica apenas como um ID hexadecimal difícil de lembrar. Nomear torna o fluxo
de trabalho muito mais prático.

```bash
# Criar image com nome e tag
docker build -t feedback-node:latest .
docker build -t feedback-node:v2 .

# Rodar container com nome personalizado
# O argumento --rm instrui o Docker a remover automaticamente o container assim que ele for parado.
docker run -d --rm -p 3000:80 --name feedback-app feedback-node:latest

# Renomear uma image existente
docker tag feedback-node:latest meu-usuario/feedback-node:prod
```

---

### Categorias de Dados (`#43`)

- **Application data** → armazenada na image (read-only, vai junto com o código)
- **Temporary data** → armazenada no container (perdida ao parar/remover o container)
- **Permanent data** → armazenada em volumes (persiste independente do ciclo do container)

---

### Volumes (`#47`)

**O que são:** Volumes são pastas no disco do host que são montadas ("mapeadas") dentro dos containers.

**Para que servem:** Garantir que dados importantes (uploads, banco de dados, logs) não sejam perdidos quando um
container for parado ou removido.

#### Gerenciando volumes

```bash
docker volume ls                   # Lista todos os volumes existentes
docker volume rm feedback          # Remove o volume chamado "feedback"
docker volume prune                # Remove todos os volumes não utilizados por nenhum container
docker volume inspect feedback     # Inspeciona detalhes de um volume (onde está no host, etc.)
```

---

#### Named Volumes (`#49`)

**O que são:** Volumes com nome definido pelo usuário, gerenciados pelo Docker.

**Para que servem:** Persistir dados entre reinicializações e remoções de container. Mesmo que o container seja
deletado, o volume (e seus dados) continuam existindo.

```bash
# Cria o container com um named volume: "feedback" mapeado para "/app/feedback" no container
docker run -d -p 3000:80 --rm --name feedback-app \
       -v feedback:/app/feedback \
       feedback-node:volumes

# Ao rodar novamente, o mesmo volume é reutilizado automaticamente
docker run -d -p 3000:80 --rm --name feedback-app \
       -v feedback:/app/feedback \
       feedback-node:volumes
```

> **`-v <nome-do-volume>:<caminho-no-container>`** — se o volume não existir, o Docker cria automaticamente.

---

#### Anonymous Volumes e como removê-los (`#50`)

**O que são:** Volumes sem nome, criados automaticamente pelo Docker. São identificados apenas por um ID longo.

**Para que servem:** Proteger pastas específicas do container de serem sobrescritas por um bind mount (ex:
`node_modules`).

```bash
# O volume anônimo é removido junto com o container ao usar --rm
docker run --rm -v /app/node_modules minha-app

# Se o container não foi criado com --rm, o volume anônimo persiste e precisa ser removido manualmente
docker volume rm abc123def456
docker volume prune    # Remove todos os volumes anônimos não utilizados
```

---

### Bind Mounts (`#51`, `#52`)

**O que são:** Mapeamento direto de uma pasta do host para dentro do container.

**Para que servem:** Desenvolvimento local — você edita o código no host e o container vê as mudanças em tempo real, sem
precisar rebuildar a image a cada alteração.

```bash
docker run \
  -p 3000:80 \
  -d \
  --rm \
  --name feedback-app \
  -v feedback:/app/feedback \
  -v "$(pwd):/app" \
  -v /app/node_modules \
  feedback-node:volumes
```

| Flag                        | O que faz                                                                                         |
|-----------------------------|---------------------------------------------------------------------------------------------------|
| `-d`                        | Modo detached — roda em background e libera o terminal                                            |
| `--rm`                      | Remove o container automaticamente quando parado                                                  |
| `-v feedback:/app/feedback` | Named volume — persiste os arquivos de feedback mesmo após remover o container                    |
| `-v "$(pwd):/app"`          | Bind mount — espelha o código do host no container em tempo real                                  |
| `-v /app/node_modules`      | Anonymous volume — protege a pasta `node_modules` do container de ser sobrescrita pelo bind mount |

---

### Resumo Volumes & Bind Mounts (`#55`)

| Tipo             | Gerenciado por | Persistência                 | Caso de uso típico                                         |
|------------------|----------------|------------------------------|------------------------------------------------------------|
| Anonymous Volume | Docker         | Até o container ser removido | Proteger pastas internas do container (ex: `node_modules`) |
| Named Volume     | Docker         | Permanente                   | Dados de banco, uploads, arquivos gerados pela aplicação   |
| Bind Mount       | Usuário        | Permanente                   | Desenvolvimento com live reload                            |

### Volumes vs Bind Mounts — Qual a diferença?

Ambos servem para persistir ou compartilhar dados entre o host e o container, mas funcionam de formas bastante
diferentes.

**Volume** é gerenciado inteiramente pelo Docker. Você não sabe (nem precisa saber) onde os arquivos ficam no disco do
host — o Docker cuida disso. É a abordagem recomendada para dados que a aplicação gera em produção (banco de dados,
uploads, logs).

**Bind Mount** é um espelho direto de uma pasta que você escolhe no host. O container enxerga exatamente os arquivos
daquele caminho em tempo real. É ideal para desenvolvimento, onde você quer que o container reflita as suas edições de
código instantaneamente.

| Aspecto              | Volume (Named)                                    | Bind Mount                                           |
|----------------------|---------------------------------------------------|------------------------------------------------------|
| Quem controla o path | Docker (path interno gerenciado automaticamente)  | Você (define o caminho absoluto no host)             |
| Visibilidade         | Opaco — não é fácil acessar direto no host        | Transparente — qualquer arquivo do host fica visível |
| Uso principal        | Produção — dados persistentes da aplicação        | Desenvolvimento — live reload do código fonte        |
| Compartilhamento     | Pode ser compartilhado entre containers           | Compartilha uma pasta específica do host             |
| Inicialização        | Docker popula com conteúdo existente no container | Sobrescreve o conteúdo do container com o do host    |
| Performance          | Melhor no Docker Desktop (Mac/Windows)            | Pode ser mais lento no Mac/Windows                   |
| Segurança            | Container não acessa o resto do host              | Container tem acesso à pasta do host escolhida       |

#### Quando usar cada um

- Use **Named Volume** quando quiser que os dados **sobrevivam** ao container mas não precisar editar esses arquivos
  diretamente no host (ex: dados do MongoDB, arquivos de upload de usuários).
- Use **Bind Mount** quando quiser **sincronizar código em desenvolvimento** — edita no VS Code, o container vê na hora,
  sem rebuildar a image.
- Use **Anonymous Volume** para **proteger** uma subpasta do container de ser sobrescrita pelo bind mount (ex:
  `node_modules` do container não deve ser substituído pelo `node_modules` vazio do host).

#### Preciso ler e gravar dados gerados pelo container diretamente no host — qual usar?

Depende de quanto acesso você precisa ter aos arquivos fora do container:

| Necessidade                                                     | Solução recomendada                                     |
|-----------------------------------------------------------------|---------------------------------------------------------|
| Acessar, mover ou processar os arquivos diretamente no host     | **Bind Mount**                                          |
| Só persistir os dados entre reinicializações, sem acesso direto | **Named Volume**                                        |
| Produção com dados sensíveis                                    | **Named Volume** (container não acessa o resto do host) |

**Se precisar de acesso direto → Bind Mount:**

```bash
# Os arquivos que o container gravar em /app/output aparecerão em /home/usuario/saida no host
docker run -v /home/usuario/saida:/app/output minha-app
```

Você define o caminho no host, o container grava, e os arquivos ficam disponíveis imediatamente — sem nenhum comando
Docker para lê-los.

**Se só precisar de persistência → Named Volume:**

```bash
# Docker escolhe onde guardar no host (normalmente /var/lib/docker/volumes/)
docker run -v meus-dados:/app/output minha-app

# Para copiar um arquivo específico para fora do container:
docker cp meu-container:/app/output/arquivo.txt ./arquivo.txt
```

> **Bind Mount** é a única opção onde você tem controle total sobre o caminho e lê os arquivos diretamente no host, como
> qualquer outra pasta normal do sistema.

#### Exemplo combinando os três

```bash
docker run \
  -v feedback:/app/feedback \   # Named volume: persiste os uploads
  -v "$(pwd):/app:ro" \         # Bind mount: sincroniza o código (read-only para o container)
  -v /app/node_modules \        # Anonymous volume: protege o node_modules do container
  feedback-node
```

> **Regra prática:** em desenvolvimento use bind mount para o código + named volume para os dados. Em produção, use
> apenas named volumes (sem bind mounts, pois o código deve estar dentro da image).


---

### Read-Only Volumes (`#56`)

**O que é:** Um bind mount marcado como somente leitura para o container.

**Para que serve:** Compartilhar o código fonte com o container sem permitir que ele escreva ou altere arquivos no host.
Boa prática de segurança em produção.

```bash
# ":ro" ao final torna o volume read-only para o container
docker run -v "$(pwd):/app:ro" minha-app

# O container pode LER os arquivos mas NÃO pode gravar no host
# Útil para separar: bind mount do código (ro) + named volume para dados gerados (rw)
docker run \
  -v "$(pwd):/app:ro" \
  -v /app/temp \
  minha-app
```

---

### Compartilhamento de volumes entre múltiplos containers

**Quando usar:** quando mais de um container precisa acessar os mesmos arquivos — ex: Airflow, onde scheduler, worker e
webserver precisam ler as mesmas DAGs e escrever nos mesmos logs.

**Como funciona:** basta montar o **mesmo named volume** em cada container. O Docker garante que todos apontam para o
mesmo diretório físico no host.

```bash
docker run -v airflow-dags:/opt/airflow/dags scheduler ...
docker run -v airflow-dags:/opt/airflow/dags worker ...
docker run -v airflow-dags:/opt/airflow/dags webserver ...
```

#### No Docker Compose

```yaml
services:
  scheduler:
    image: apache/airflow
    volumes:
      - airflow-dags:/opt/airflow/dags
      - airflow-logs:/opt/airflow/logs

  worker:
    image: apache/airflow
    volumes:
      - airflow-dags:/opt/airflow/dags
      - airflow-logs:/opt/airflow/logs

  webserver:
    image: apache/airflow
    volumes:
      - airflow-dags:/opt/airflow/dags
      - airflow-logs:/opt/airflow/logs

volumes:
  airflow-dags:   # declarado uma vez, compartilhado por todos os serviços
  airflow-logs:
```

#### Pontos de atenção

| Situação                                  | Cuidado                                                                         |
|-------------------------------------------|---------------------------------------------------------------------------------|
| Dois containers escrevem no mesmo arquivo | Pode gerar conflito — evitar ou usar controle de concorrência                   |
| Produção multi-host (cluster)             | Named volume é local; clusters precisam de storage distribuído (NFS, EFS, etc.) |
| Usuários diferentes entre containers      | Pode gerar conflito de permissões (`chown`)                                     |

#### Bind mount como alternativa em desenvolvimento

```yaml
volumes:
  - ./dags:/opt/airflow/dags   # todos os serviços veem as mesmas DAGs editadas no host
```

> **Regra:** use **Named Volume** quando os dados precisam ser compartilhados e sobreviver aos containers. Use **Bind
> mount** em desenvolvimento quando quiser editar os arquivos diretamente do host.

---

### Environment Variables & `.env` (`#61`)

**O que são:** Variáveis de ambiente que configuram o comportamento da aplicação em tempo de execução.

**Para que servem:** Evitar hardcode de configurações (porta, credenciais, URLs) dentro do código. Permitem usar a mesma
image em diferentes ambientes (dev, staging, prod) apenas trocando as variáveis.

```dockerfile
# No Dockerfile: define valor padrão
ENV PORT 80
EXPOSE $PORT
```

```bash
# Passando variável diretamente no comando
docker run --env PORT=8000 -p 8000:8000 minha-app

# Passando múltiplas variáveis
docker run --env PORT=8000 --env NODE_ENV=production minha-app

# Usando arquivo .env (recomendado para múltiplas variáveis)
docker run --env-file ./.env minha-app
```

```bash
# Exemplo de arquivo .env
PORT=8000
NODE_ENV=production
DB_HOST=mongodb
```

> Nunca versione arquivos `.env` com credenciais reais — adicione ao `.gitignore` e `.dockerignore`.

---

### Build Arguments — ARG (`#63`)

**O que são:** Variáveis disponíveis apenas durante o processo de build da image (não existem em tempo de execução).

**Para que servem:** Criar variações da mesma image sem duplicar o Dockerfile — por exemplo, uma versão de dev e uma de
prod com configurações diferentes.

```dockerfile
# No Dockerfile
ARG DEFAULT_PORT=80
ENV PORT $DEFAULT_PORT
EXPOSE $PORT
```

```bash
# Build padrão (usa DEFAULT_PORT=80)
docker build -t feedback-node:web-app .

# Build com argumento customizado (porta 8000)
docker build -t feedback-node:dev --build-arg DEFAULT_PORT=8000 .

# Resultado: duas images com configurações diferentes, mesmo Dockerfile
```

> **Diferença ARG vs ENV:** `ARG` existe só no build. `ENV` existe no build e em tempo de execução dentro do container.

---

## Section 4: Networking — Cross-Container Communication

### Casos de Uso (`#67–69`)

1. **Container → WWW** — funciona por padrão, sem configuração extra
2. **Container → Host Machine** — substituir `localhost` por `host.docker.internal`
3. **Container → Container** — via Docker Networks (containers na mesma network enxergam-se pelo nome)

---

### Inspecionar container (`#73`)

**O que é:** Exibe todas as informações de configuração de um container em formato JSON.

**Para que serve:** Descobrir o IP do container, volumes montados, variáveis de ambiente, network, configurações de
restart, entre outros detalhes internos.

```bash
docker container inspect mongo
# Retorna JSON com: IPAddress, Mounts, Env, NetworkSettings, etc.

# Para pegar só o IP:
docker container inspect mongo | grep IPAddress
```

---

### Docker Networks (`#74`)

**O que são:** Redes virtuais internas do Docker que conectam containers entre si.

**Para que servem:** Permitir comunicação entre containers de forma segura e sem precisar expor portas para o host.
Dentro da mesma network, um container pode referenciar outro pelo nome como se fosse um hostname DNS.

```bash
# Criar uma network
docker network create favorites-net

# Listar todas as networks
docker network ls

# Conectar um container à network ao criá-lo
docker run -d --rm --name mongodb --network favorites-net mongo

# Na aplicação, usar o nome do container como host:
# mongoose.connect("mongodb://mongodb:27017/favorites")
#                              ^^^^^^^ nome do container
```

> Dentro de uma mesma network, containers se comunicam pelo **nome do container** como hostname — o Docker resolve o IP
> automaticamente.

---

## Section 5: Multi-Container Applications

### Setup da aplicação (`#80–83`)

Aplicação com 3 serviços rodando em containers separados:

- **MongoDB** — banco de dados
- **Node.js** — backend/API
- **React** — frontend (SPA)

```bash
# React precisa de modo interativo (-it) para o processo de build não encerrar
docker run -it minha-react-app
```

### Boas práticas aplicadas (`#86–88`)

- Volumes para persistência do MongoDB (dados não são perdidos ao reiniciar)
- Autenticação com `MONGO_INITDB_ROOT_USERNAME` / `MONGO_INITDB_ROOT_PASSWORD`
- Bind mount + nodemon no Node para live reload durante desenvolvimento
- `.dockerignore` para excluir `node_modules` do contexto de build
- Variáveis de ambiente via `ENV` no Dockerfile e `--env` no `docker run`

---

## Section 6: Docker Compose — Multi-Container Orchestration

### O que é e por que usar (`#92`)

Docker Compose permite definir e gerenciar múltiplos containers em um único arquivo `docker-compose.yml`, substituindo
múltiplos comandos `docker run` longos e difíceis de lembrar.

**Benefícios:**

- Um único comando sobe toda a stack (`docker compose up`)
- Networks entre os serviços são criadas automaticamente
- Volumes e variáveis de ambiente ficam centralizados e versionados

---

### Estrutura do `docker-compose.yml` (`#93–94`)

```yaml
version: "3.8"
services:
  mongodb:
    image: mongo
    volumes:
      - data:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: max
      MONGO_INITDB_ROOT_PASSWORD: secret

  backend:
    build: ./backend      # constrói a image a partir do Dockerfile em ./backend
    ports:
      - "80:80"
    depends_on:
      - mongodb            # garante que o mongodb sobe antes do backend

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend

volumes:
  data:                   # declara o named volume usado pelo mongodb
```

---

### Comandos do Docker Compose (`#96`)

**O que são:** Comandos para gerenciar o ciclo de vida de toda a stack definida no `docker-compose.yml`.

**Para que servem:** Subir, parar, rebuildar e destruir todos os serviços com um único comando, em vez de gerenciar cada
container individualmente.

```bash
# Sobe todos os serviços (cria containers, networks e volumes)
docker compose up

# Sobe em modo detached (background)
docker compose up -d

# Força rebuild de todas as images antes de subir
docker compose up --build

# Para os containers e remove (volumes são mantidos)
docker compose down

# Para e remove containers + volumes (CUIDADO: apaga dados persistentes)
docker compose down -v

# Ver logs de todos os serviços
docker compose logs

# Ver logs de um serviço específico
docker compose logs backend
```

---

### Subir apenas alguns serviços (`#119`)

**Para que serve:** Útil quando você quer subir apenas parte da stack — por exemplo, os serviços principais sem os
utilitários.

```bash
# Sobe apenas os serviços listados (e suas dependências via depends_on)
docker compose up -d server php mysql

# Rodar um comando pontual em um serviço (ex: utility container)
docker compose run --rm composer install
```

---

### Nomes de containers (`#99`)

**Para que serve:** Por padrão o Docker Compose gera nomes automáticos como `projeto_backend_1`. Definir
`container_name` facilita referências em scripts e logs.

```yaml
services:
  backend:
    build: ./backend
    container_name: meu-backend   # nome fixo em vez do gerado automaticamente
```

---

## Section 7: Utility Containers

### O que são e por que usar (`#102–103`)

Containers usados **apenas para executar um comando ou ferramenta** (ex: `npm`, `composer`, `artisan`), sem precisar
instalar nada no host. Após executar, o container é removido automaticamente.

**Problema que resolvem:** Em equipes com diferentes sistemas operacionais ou versões de ferramentas, o utility
container garante que todos usem exatamente a mesma versão da ferramenta, sem poluir o ambiente local.

---

### Usando ENTRYPOINT (`#106`)

**O que é:** Define o executável padrão do container. Diferente de `CMD`, o `ENTRYPOINT` não é sobrescrito ao passar
argumentos — os argumentos são *adicionados* a ele.

**Para que serve:** Fazer o container se comportar como se fosse a própria ferramenta (ex: qualquer argumento passado ao
`docker run` é enviado direto para o `npm`).

```dockerfile
# Dockerfile do utility container
FROM node:18-alpine
WORKDIR /app
ENTRYPOINT ["npm"]
```

```bash
# Sem ENTRYPOINT, precisaria passar "npm" explicitamente:
docker run --rm node-util npm install

# Com ENTRYPOINT ["npm"], basta passar o subcomando:
docker run --rm node-util install   # → executa: npm install
docker run --rm node-util init      # → executa: npm init
docker run --rm node-util run dev   # → executa: npm run dev
```

---

### Com Docker Compose (`#107`)

**Para que serve:** Rodar comandos de utility containers sem precisar lembrar do comando `docker run` completo.

```yaml
# docker-compose.yml
services:
  npm:
    build: ./docker/npm
    volumes:
      - ./:/app
```

```bash
# Roda o serviço "npm" com o subcomando "install"
docker compose run --rm npm install

# Instalar um pacote específico
docker compose run --rm npm install express

# Inicializar projeto
docker compose run --rm npm init -y
```

---

## Section 8: Laravel & PHP Project (Setup Complexo)

### Serviços do projeto (`#112–121`)

| Serviço  | Tipo      | Função                                          |
|----------|-----------|-------------------------------------------------|
| Nginx    | Principal | Web server — recebe as requisições HTTP         |
| PHP-FPM  | Principal | Interpretador PHP — processa os arquivos `.php` |
| MySQL    | Principal | Banco de dados relacional                       |
| Composer | Utility   | Instalar dependências PHP (via `composer.json`) |
| Artisan  | Utility   | CLI do Laravel (migrations, seeds, etc.)        |
| npm      | Utility   | Compilar assets de frontend                     |

```bash
# Subir apenas os serviços principais (sem os utilities)
docker compose up -d server php mysql

# Criar o projeto Laravel usando o utility container do Composer
docker compose run --rm composer create-project laravel/laravel .

# Rodar migrations
docker compose run --rm artisan migrate

# Instalar dependências JS
docker compose run --rm npm install
```

---

## Section 9: Deploying Docker Containers

### Desenvolvimento → Produção (`#125–126`)

**Diferenças principais:**

| Aspecto   | Desenvolvimento          | Produção                              |
|-----------|--------------------------|---------------------------------------|
| Código    | Bind mount (live reload) | Copiado para dentro da image (`COPY`) |
| Variáveis | `.env` local             | Variáveis de ambiente do servidor     |
| Volumes   | Bind mounts e named      | Apenas named volumes para dados       |
| Providers | —                        | AWS, Google Cloud, Azure              |

---

### Instalar Docker na instância EC2 (`#131`)

**O que é:** Sequência de comandos para instalar e configurar o Docker em uma instância Amazon Linux (EC2).

**Para que serve:** Preparar o servidor remoto para rodar containers, adicionando o usuário `ec2-user` ao grupo `docker`
para não precisar de `sudo` em cada comando.

```bash
sudo yum update -y                          # Atualiza os pacotes do sistema
sudo yum -y install docker                  # Instala o Docker
sudo service docker start                   # Inicia o serviço do Docker
sudo usermod -a -G docker ec2-user          # Adiciona o usuário ao grupo docker (sem sudo)
sudo systemctl enable docker               # Configura o Docker para iniciar automaticamente
docker version                              # Verifica se a instalação foi bem-sucedida
```

> Após `usermod`, reconecte via SSH para que a mudança de grupo tenha efeito.

---

### Publicar e executar image na nuvem (`#134–135`)

**O que é:** Fluxo completo de build → push para o Docker Hub → pull e execução no servidor remoto.

**Para que serve:** Disponibilizar a aplicação na instância EC2 sem precisar copiar o código manualmente — a image já
contém tudo.

```bash
# --- Na sua máquina local ---

# Build da image com tag do Docker Hub
docker build -t meu-usuario/minha-app:latest .

# Login no Docker Hub (solicita usuário e senha)
docker login

# Envia a image para o Docker Hub
docker push meu-usuario/minha-app:latest

# --- Na instância EC2 (via SSH) ---

# Baixa a image do Docker Hub
docker pull meu-usuario/minha-app:latest

# Executa o container expondo a porta 80
docker run -d -p 80:80 meu-usuario/minha-app:latest
```

#### Habilitar acesso externo (`#135`)

1. No console AWS, acesse **EC2 → Security Groups**
2. Selecione o Security Group da instância
3. Adicione regra de **Inbound**: Tipo `HTTP`, Porta `80`, Source `0.0.0.0/0`

---

### Atualizar a aplicação em produção (`#136`)

```bash
# Local: rebuild com nova versão
docker build -t meu-usuario/minha-app:latest .
docker push meu-usuario/minha-app:latest

# EC2: parar o container antigo, baixar nova versão e subir
docker stop <container-id>
docker pull meu-usuario/minha-app:latest
docker run -d -p 80:80 --rm meu-usuario/minha-app:latest
```

---

### Desvantagens da abordagem manual (`#137`)

- Atualização manual a cada nova versão
- Sem auto-scaling (não aguenta picos de tráfego automaticamente)
- Sem health checks automáticos (container com crash não reinicia sozinho)
- Gerenciamento de múltiplos containers complexo

**Solução:** Usar serviços gerenciados como AWS ECS.

---

### AWS ECS — Managed Service (`#140`)

**O que é:** Elastic Container Service — serviço da AWS que gerencia o ciclo de vida dos containers automaticamente.

**Para que serve:** Eliminar a complexidade do gerenciamento manual. O ECS cuida de orquestração, scaling automático,
load balancing, health checks e reinicialização de containers com falha.

---

## Tips & Referências

- **Mounting Docker volumes with Docker Toolbox for Windows:**
  <https://headsigned.com/posts/mounting-docker-volumes-with-docker-toolbox-for-windows/>