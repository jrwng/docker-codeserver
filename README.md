# docker-codeserver

A Visual Studio Code remote development environment image, connect by Remote - SSH extension.

## Supported tags and respective Dockerfile links

* [1.0.0-jdk-17, latest](https://github.com/jrwng/docker-codeserver/blob/567a13dc6d1ce567af35fffb8f92d9036fe9d54f/java/17/Dockerfile)
* [1.0.0-jdk-11](https://github.com/jrwng/docker-codeserver/blob/567a13dc6d1ce567af35fffb8f92d9036fe9d54f/java/11/Dockerfile)

## How to use this image

### Install Code Server using Docker Engine

1. Prepare the maven settings.xml file, or delete settings.xml volume if you don't use a internal Maven Repository Manager, then run the image: 
   
    ``` shell
    docker run --detach \
      --publish 3600:22 \
      --name codeserver \
      --restart always \
      --volume $HOME/workspaces:/root/workspaces \
      --volume $HOME/.m2/repository:/root/.m2/repository \
      --volume $HOME/.m2/settings.xml:/root/.m2/settings.xml \
      --volume $HOME/.vscode-server/extensions:/root/.vscode-server/extensions \
      --volume $HOME/.vscode-server/data:/root/.vscode-server/data \
      jrwng/codeserver-java:latest
    ```

2. Enter the container to set the password or add the public key
    
    ``` shell
    docker exec -it codeserver bash
    ```

### Install Code Server using Docker swarm mode

1. Set up a Docker swarm.
2. Create a docker-compose.yml file:

    ``` ymal
    version: "3.8"
    services:
    
      mariadb:
        image: mariadb:10.4.12-bionic
        environment:
          MYSQL_ROOT_PASSWORD: LTyYnEn:epg4!Fg5tL~-
          TZ: Asia/Shanghai
        volumes:
          - mariadb-data:/var/lib/mysql
        configs:
          - source: mariadb.cnf
            target: /etc/mysql/conf.d/ext.cnf
    
      redis:
        image: redis:6.0.1
        environment:
          TZ: Asia/Shanghai
        volumes:
           - redis-data:/data
        configs:
          - source: redis.conf
            target: /usr/local/etc/redis/redis.conf
        command: ["redis-server", "/usr/local/etc/redis/redis.conf"]
    
      codeserver:
        image: jrwng/codeserver-java:latest
        environment:
          TZ: Asia/Shanghai
        ports:
          - "3600:22"
        configs:
          - source: id_rsa.pub
            target: /root/.ssh/authorized_keys
            mode: 0600
          - source: maven-settings.xml
            target: /root/.m2/settings.xml
          - source: gitconfig
            target: /root/.gitconfig
          - source: git-credentials
            target: /root/.git-credentials
        volumes:
          - workspaces:/root/workspaces
          - maven-repository:/root/.m2/repository
    
    configs:
      id_rsa.pub:
        file: ./id_rsa.pub
      maven-settings.xml:
        file: ./maven-settings.xml
      mariadb.cnf:
        file: ./mariadb.cnf
      redis.conf:
        file: ./redis.conf
      gitconfig:
        file: ./gitconfig
      git-credentials: 
        file: ./git-credentials
    
    volumes:
      mariadb-data:
      redis-data:
      workspaces:
      maven-repository: 
      vscode-server-extensions:
      vscode-server-data:
    ```

3. Create all the configuration files you need.
4. Make sure you are in the same directory as docker-compose.yml and run:
   ``` shell
   docker stack deploy -c docker-compose.yml mystack
   ```
