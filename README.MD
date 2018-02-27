
#### Spring Cloud with the help of JHipster secured with Keycloak OAuth2

Overview of containers created

- nginx
- jhipster-registry
- jhipster-gateway1
- jhipster-micro1
- postgres
- kafka
- zookeeper
- hazelcast

Once all is up, then nginx configuration needs a bit of a tweak

```bash
# go inside the container
docker exec -it nginx_server bash

# go to edit the /etc/nginx/conf.d/server1/reverseProxy.conf
location ~* ^/(.*) {
  # set $backend "http://gateway1-app:8080";
  # proxy_pass $backend$1$is_args$args;

  proxy_pass http://gateway1-app:8080;
  proxy_set_header X_FORWARDED_PROTO $scheme;
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Server $host;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header Host $host;
  port_in_redirect off;

}
```

```yml
# create a local volume for the gateway and micro1
docker volume create gateway1_postgresql_volume
docker volume create micro1_postgresql_volume

# if projects changed, below needs to be build to its latest Docker image by doing `mvn verify -Pprod dockerfile:build`
- jhipster-registry
- jhipster-gateway
- jhipster-micro1

# finally, run it
docker-compose up
```

Open the registry at http://registry-local.nellmedina.com/ and gateway at http://api-local.nellmedina.com/ with username and password as admin admin