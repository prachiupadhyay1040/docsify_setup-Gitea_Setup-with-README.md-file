##  How to set up docsify?
### To install docsify-cli
      $ npm i docsify-cli -g

### To initialise the docs folder
      $ docsify init ./docs

### To preview the site.
      $ docsify serve docs

### Create a dockerfile to build own image 
      $ cat Dockerfile
        FROM node:latest
        LABEL description="A demo Dockerfile for build Docsify."
        WORKDIR /docs
        RUN npm install -g docsify-cli@latest
        RUN npm install docsify-print
        EXPOSE 3000/tcp
        ENTRYPOINT docsify serve .

### Build the image 
      podman  build -f Dockerfile -t prachidocsify . 

### Create the container
      podman run -d -p 5000:3000 --name=docsify -v /home/prachi/docs:docs localhost/prachidocsify


## How to set up gitea? 

     ~/script$ cat gitea-pod-script.sh
     #!/bin/bash

### create a pod with name gitea and expose port 9080
      $ podman pod create --name gitea-gitea --publish 3000:3000
### create a postgres container, give it the desired environment variable, attach it to the create pod
      $ podman run -dt \
        --pod gitea-gitea \
        --name gitea-postgres \
        -e POSTGRES_DB=gitea \
        -e POSTGRES_USER=gitea \
        -e POSTGRES_PASSWORD=gitea \
        -e PGDATA=/var/lib/postgresql/data/pgdata \
        -v /home/prachi/gitea:/var/lib/postgresql/data \
        postgres
        
### create a gitea container, give it the desired environment variable, important varibles,importantly DB_VENDOR, attach it to create pod
      $ podman run -dt \
        --pod gitea-gitea \
        --name gitea-app \
        -e USER_UID=1000 \
        -e USER_GID=1000 \
        -e GITEA_database_DB_TYPE=postgres \
        -e GITEA_database_HOST=localhost:5432 \
        -e GITEA_database_NAME=gitea \
        -e GITEA_database_USER=gitea \
        -e GITEA_database_PASSWORD=gitea \
        -v /home/prachi/gitea-app:/data \
        gitea/gitea


