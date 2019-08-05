# Docker
É uma tecnologia de virtualização não tradicional (SEM VM)
É uma engine de administração de containers
Utiliza s serviços do LXC (Linux Container)
Open Source
Criada sob a linguagem GO
Sistemas de virtualização baseada em software
Host e contaier compartilham o kernel
Empacota software com vários níveis de isolamento (memoria, cpu, rede)

# Container
É um processo isolado, que apartir dele você pode startar aplicações isoladas, com sistemas de arquivos isolados. 
Ambiente controlado
Segregação de processo no mesmo Kernel (Isolamento)
Sistemas de arquivos criados a partir de uma imagem
Ambiente leve e portátil no qual aplicações são executadas
Encapsula todos os binários e bibliotecas necessárias para execucação de uma App
Algo entre um chroot e uma VM

# Docker x LXC
Docker usa tudo do LXC porém desenvolveu uma camda com CLI, REST API e Dockerfile para facilitar o gerenciamento do arquitetura LXC

# Imagem Docker
Modelo de sistema de arquivo somente-leitura usado para criar containers
Imagens são criadas através de um processo chamado build ou commit
São armazenadas em repositórios no Registry
São compostas por uma ou mais camadas (layers)
Uma camada representa uma ou mais mudanças no sistema de arquivos
Uma camada é também chamada de imagem intermediária
A junção dessas camadas formam a imagem
A apenas a última camada pode ser alterada quando o container for iniciado.
AUFS (Advanced multi-layered unification filesystem) é muito usado
O grande objetivo dessa estratégia de dividir uma imagem em camadas é o reuso.
É possível compor imagens a partir de camadas de outras imagens.

# Imagem x Container
Imagem = Classe
Container = Objeto

Através de uma imagem é possivel criar vários container
Imagem = sistema de arquivo somente-leitura
Container = processo segregado e isolado, que utiliza a imagem.

# Arquitetura
Client
    CLI
    APP (REST API)
    KITEMATIC

Docker Daemon, Engine ou Server
    Imagem -> Container

Registry

# Instalação

# Docker Client

```docker container run hello-world```
docker container ps (processos em execucao)
docker container ps -a (processo executas anteriormente)

docker container run [NOME_IMAGEM] [COMANDO] (sempre sobe um novo container)
    exemplo: ```docker container run debian bash --version```

docker container run --rm [NOME_IMAGEM] (remove para nao aparecer no ps -a)
    exemplo: ```docker container run --rm debian bash --version```

docker container run -it (modo interativo e terminal)
    exemplo: ```docker container run -it bash```

docker container run --name [NOME_IMAGEM] (define nome e quando digitado mesmo comando ele abre o container criado anteriormente)
    exemplo: ```docker container run --name mydeb -it debian bash```

docker container ls (lista container criados)
docker container ls -a (lista container criados anteriormentes)

docker container start -ai [NOME_IMAGEM]
    exemplo: ```docker container start -ai mydeb```

docker container run -p [PORTA_PUBLICA]:[PORTA_INTERNA] [IMAGEM]
    exemplo: ```docker container run -p 8080:80 nginx```


## Volume
docker container run -p [PORTA_PUBLICA]:[PORTA_INTERNA] -v [PASTA_LOCAL]:[CAMINHO_CONTAINER] [IMAGEM]
    exemplo: ```docker container run -p 8080:80 -v $(pwd)/not-found:/usr/share/nginx/html nginx```

## Container em background
docker container run -d ....
    exemplo: ```docker container run -d --name ex-daemon-basic -p 8080:80 -v $(pwd)/html:/usr/share/nginx/html nginx```

docker container stop [NOME_CONTAINER]
    exemplo: ```docker container stop ex-daemon-basic```

docker container start [NOME_CONTAINER]

docker container restart [NOME_CONTAINER]

## Manipular container em modo daemon
### Listar container em execucao
```
docker container list
docker container ls
docker container ps
docker ps
```

### Listar container ja criados
```
docker container ps -a
docker container list -a
docker container ls -a
docker ps -a
```

docker container logs [NOME_CONTAINER]
    exemplo: ```docker container logs ex-daemon-basic```

docker container inspect [NOME_CONTAINER]
    exemplo: ```docker container inspect ex-daemon-basic```

docker container exec [NOME_CONTAINER] [COMANDO]
    exemplo: ```docker container exec ex-daemon-basic uname -or```

```
docker image ls
docker volume ls
```

## Remover imagem
docker rmi [CONTAINER_ID]
docker image rm [CONTAINER_ID]

```
docker container --help
docker image --help
docker volume --help
```

```
docker image pull redis:latest
docker image ls

docker image inspect redis:latest

docker image tag redis:latest coder-redis

docker image rm redis:latest coder-redis
```
### Baixa a imagem do DockerHub
```
docker image pull

docker image ls
docker container run [NOME_IMAGEM] [COMANDO] (sempre sobe um novo container) exemplo: docker container run debian bash --version

docker container run --rm [NOME_IMAGEM] (remove para nao aparecer no ps -a) exemplo: docker container run --rm debian bash --version

docker container run -it (modo interativo e terminal) exemplo: docker container run -it bash
docker image rm

docker imagem tag
```

### Gera uma imagem
```
docker image build
```

### Publicar imagem
```
docker image push
```

# Docker Hub vs Docker Registry

```
docker image build -t ex-simple-build .

docker image build --build-arg S3_BUCKET=myapp -t ex-build-arg .

docker container run ex-build-arg bash -c 'echo $S3_BUCKET'

docker image inspect --format="{{index .Config.Labels \"maintainer\"}}" ex-build-arg

docker image build -t ex-build-copy .
docker container run -p 80:80 ex-build-copy

docker image build -t ex-build-dev .
docker container run -it -v $(pwd):/app -p 80:8000 --name python-server ex-build-dev
docker container run -it --volumes-from=python-server debian cat /log/http-server.log
```

# Docker Hub
```
docker login --username=fhziliotti

docker image tag ex-simple-build fhziliotti/simple-build:1.0
docker image push fhziliotti/simple-build:1.0
```


# REDES
Bridge Network (Padrão)
None Network
Host Network
Overlay Network (Swarm)

```
# Lista redes do docker
docker network ls

#
docker container run -d --net none debian

docker container run --rm alpine ash -c "ifconfig"

docker container run --rm --net none alpine ash -c "ifconfig"

docker network inspect bridge

docker container run -d --name container1 alpine sleep 1000
docker container exec -it container1 ifconfig

docker container run -d --name container2 alpine sleep 1000
docker container exec -it container2 ifconfig

docker container exec -it container2 ping 172.17.0.2

# Criar Rede nova
docker network create --driver bridge rede_nova

docker container inspect container1

docker container run -d --name container3 --net rede_nova alpine sleep 1000

docker network connect bridge container3

docker container exec -it container3 ping 172.17.0.2

docker network disconnect bridge container3
```


#Docker- Compose
```
docker-compose up -d

docker-compose ps

docker-compose exec db psql -U postgres -c '\l'

docker-compose down

docker-compose exec db psql -U postgres -f /scripts/check.sql

docker-compose logs -f -t

docker-compose up -d --scale worker=3
```

Script de inicialização no postgres
`/docker-entrypoint-initdb.d/init.sql`
