# Proxy reverso - conexão Postgresql

O arquivo `compose.yaml` contém configurações para criação de containers Docker:

* Postgresql (banco de dados)
* Adminer (gerenciador do banco de dados)
* Nginx (servidor web para o proxy reverso)

Ao executar o comando `docker-compose up` (no diretório do arquivo `compose.yaml`), os três containers serão criados. O container do Nginx tem um volume mapeado para uma configuração externa (no host). 

## Configuração do proxy

O mapeamento configurado no volume do NGINX para o arquivo `nginx.conf` contém a sessão abaixo. Ela faz com que as tentativas de conexão ao banco de dados na porta 5435 sejam direcionadas ao banco de dados, que expõe a porta 5432.

```
stream {    
    upstream database_test {
        server db:5432;
    }
    server {
        listen 5435 so_keepalive=on;
        proxy_pass database_test;

    }
}
```

Para testar o proxy:

* no navegador, execute o adminer acessando `http://localhost:8080/`
* insira os valores abaixo e clique no botão `Login`:
  * System: PostgreSQL
  * Server: nginx:5435
  * Username: postgres
  * Password: example
  * Database: postgres

Pode-se também configurar mais opções de segurança no proxy, permitindo apenas conexões de determinados IPs : 

```
allow <ip_address>;
deny all;

proxy_connect_timeout 60s;
proxy_socket_keepalive on;
```

## Referências: 

* https://wasi0013.com/2021/11/15/setup-nginx-reverse-proxy-to-access-postgresql-database-remotely/

* https://serverfault.com/questions/1082136/connect-to-postgres-database-behind-nginx-reverse-proxy-using-docker-containers
