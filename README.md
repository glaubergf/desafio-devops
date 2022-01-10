# Desafio Devops

O projeto contém uma aplicação básica com Node, Ngnix e MySQL. 

A cada atualização da página, um novo registro será cadastrado no banco de dados e será mostrado na listagem, na mesma página.  

O projeto contém algumas falhas e erros, analise e implemente as devidas correções.

Se não entender algum conceito ou parte do problema, não é motivo para se preocupar! Queremos que faça o desafio até onde souber.

### O que deve ser feito? ### 

 - ajustes que fazem todas as aplicações subirem e se comunicarem
 - um README contendo os seus pensamentos ao longo do projeto para identificação e correção dos erros

Faça um fork e realize commits ao longo do processo para que possamos entender o seu modo de pensar! :)

##
**d:' )**
## 

# Troubleshooting

Veja abaixo a resolução feita para as aplicações estarem de pé e se comunicando conforme o esperado.

## nginx

Foi acrescentado a linha **COPY** no *Dockerfile* do nginx.

```
COPY nginx.conf /etc/nginx/conf.d
```

**COPY** -> *Copia arquivos e diretórios e os adicionam ao filesystem do container.*

Já no arquivo *nginx.conf* foi acrescentado na linha 5 a porta '**:3000**' da aplicação node.


```
proxy_pass http://app:3000;
```

## mysql

Não foi preciso fazer nenhuma alteração no diretório mysql do projeto.

## node

No *Dockerfile* do node, foi editado a 1ª linha **COPY** para copiar apenas os files package*.json do diretório ./node para o filesystem do container.

```
COPY package*.json ./
```

Após o RUN que faz o downlaod do dockerize, foi acrescentado o **COPY** para copiar todo conteúdo do diretório raiz onde esta o Dockerfile para o diretório de trabalho *(/usr/src/app)* do container. Em seguida foi acrescentado outro **RUN** para execução do *npm install*.

```
COPY . .

RUN npm install
```

Finalizando o *Dockerfile*, foi acrescentado o **CMD** para executar os comandos *npm run start* no início da execução do container.

```
CMD ["npm","run","start"]
```

**RUN** -> *Executa qualquer comando em uma nova camada no topo da imagem e "commita" as alterações. Essas alterações poderá ser utilizar nas próximas instruções do Dockerfile.*

**CMD** -> *Executa um comando, diferente do RUN que executa o comando no momento em que está "buildando" a imagem, o CMD executa no início da execução do container.*

No arquivo *connectionDb.js* foi editado apenas o nome da base de dados: de *nodedb* para **node_db**.

No arquivo *index.js* foi acrescentado o separador de declarações " **;** " no final de cada linha declarada onde não tinha a mesma.

No arquivo *routes.js* foi acrescentado o " **;** "  no final das linhas **17** e **21**.

**Nota**: *JavaScript é uma linguagem que faz declarações ao navegador. O carácter utilizado para separar essas declarações é " **;** " e o mesmo deve ser sempre utilizado.*

Já no *docker-compose.yaml* foi necessário acrescentar o bloco **networks** para criar a interface de rede de comunicação entre os containers.

```
networks:
  node-network:
    driver: bridge
```

Após todos as alterações feitas, para testar a execução pelo próprio console, execute o comando **curl** com o loop **for** numa sequência de **2 vezes** ou mais, gravando assim no arquivo de saída chamado *desafiodevops.txt*.

```
for i in $(seq 2); do $(curl http://localhost:3000/ > desafiodevops.txt); done; ls desafiodevops.txt; cat desafiodevops.txt
```
