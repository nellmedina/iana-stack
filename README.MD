
### Docker compose file to run JHipster microservices secured by Keycloak OAuth2

Overview of containers created where most of it where auto-generated from [JHipister Starter](https://start.jhipster.tech/).

- nginx
- [jhipster-registry](https://github.com/nellmedina/jhipster-registry)
- [gateway1](https://github.com/nellmedina/gateway1)
- [micro1](https://github.com/nellmedina/micro1)
- postgres
- kafka
- zookeeper
- hazelcast

#### Execute the Docker Compose file

```yml
# create a local volume for the microservices named gateway1 and micro1
docker volume create gateway1_postgresql_volume
docker volume create micro1_postgresql_volume

# if projects changed, below needs to be build to its latest Docker image by doing `mvn verify -Pprod dockerfile:build`
# for registry, docker build it like `mvn verify -Pprod docker:build`
- jhipster-registry
- gateway1
- micro1

# stop and delete the container to be updated so it will get agin the latest build
Example:
docker stop gateway1
docker rm gateway1

# finally, run it
docker-compose up
```

If Nginx not redirecting properly, then adjust it a bit.

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

# restart nginx_server
docker restart nginx_server
```

Open the registry at http://registry-local.nellmedina.com/ and gateway at http://api-local.nellmedina.com/ with username and password as admin admin.

Note that for OAuth2 authentication, login page will be the Keycloak and redirected back to app with Token if successfully authenticated.