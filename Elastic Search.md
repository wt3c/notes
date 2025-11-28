
# Elastic Search  
  
## Instalaçao  
  
Utilizei **Docker** para simplificar a operaçao,   
Seguindo a [documentaçao oficial](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html), testei com um amabiente mais proximo de produçao, com 3 nós mais o Kibana,  
utilizei a **versao 8.15.0**  
Baixei pela documentacão os seguintes arquivos  
  
[.env](https://github.com/elastic/elasticsearch/blob/8.15/docs/reference/setup/install/docker/.env)  
  
[docker-compose](https://github.com/elastic/elasticsearch/blob/8.15/docs/reference/setup/install/docker/docker-compose.yml)  
  
Alteri somente o .env, alteções importantes estão no arquivo env-sample pode seguir com base  
  
### vm.max_map_count  
  
Configure o vm.max_map_count para pelo menos 262144  
  
#### No linux  
  
```shell  
sudo sysctl -w vm.max_map_count=262144  
```  
  
#### Windows [with Docker Desktop WSL 2 backend](https://docs.docker.com/desktop/wsl/)  
  
```shell  
wsl -d docker-desktop -u rootvm.max_map_count = 262144```  
  
## Conetor  
  
É uma ferramenta para sincronizar várias bases com Elastic Search.  
  
**Antes de qualquer coisa**  
  
- Crie um index  
  - No console do Kibana entre com o comando  
  ```shell  
    PUT /SEU-INDEX/_settings        {  
            "index.highlight.max_analyzed_offset": 4000000  
        }  
    ```  
    
 ```shell  
	  git clone  https://github.com/elastic/connectors.git  
  ```  
- Renomei o arquivo `config.yml.example` para `config.yml`  
- Na aba a esquerda no Kibana, vai em `Connectors` e adicione o novo conector  
- Durante o processo escolha a opção Git  
- Clone o projeto  
- Copie a configuração exibida para o arquivo `config.yml`  
    - Altere a http para https  
  - Na sessão SSL  
  - Habilita o SSL  
        - `elasticsearch.ssl: true`  
    - adicione o caminho completo do certificado em  
        - `elasticsearch.ca_certs: path/ca.crt`  
- Configure a conexão com o banco de dados.  
  
**Para pegar os certificados**  
```shell  
docker cp mplab-es01-1:/usr/share/elasticsearch/config/certs/ .
```



# Upload
- Criando o Index com Schema
```shell  
curl -H 'Content-Type: application/json' -XPUT https://127.0.0.1:9200/biblia --data-binary @pt_aa_schema.json  -u elastic:SENHA --cacert ~/certs/ca/ca.crt	 
 ``` 
 
 - Upload do arquivo
 ```shell  
 1. wget http://media.sundog-soft.com/es7/shakes-mapping.json

3. curl -H 'Content-Type: application/json' -XPUT 127.0.0.1:9200/shakespeare --data-binary @shakes-mapping.json

1. wget http://media.sundog-soft.com/es7/shakespeare_7.0.json

3. curl -H 'Content-Type: application/json' -XPOST '127.0.0.1:9200/shakespeare/_bulk?pretty' --data-binary
4. @shakespeare_7.0.json

1. curl -H 'Content-Type: application/json' -XGET
2. '127.0.0.1:9200/shakespeare/_search?pretty' -d '
3. {
4. "query" : {
5. "match_phrase" : {
6. "text_entry" : "to be or not to be"
7. }
8. }
9. }
10. '
 ```
