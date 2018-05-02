---
title: docker memo
date: 2016-02-11
---

usually i'm confused which docker command to use...

* docker : command for docker container
* docker build -f /path/to/Dockerfile : build docker image via Dockerfile
* docker-machine : command for host machine of docker
* docker-compose : set up containers via docker-compose.yml

```

# build image via Dockerfile
> docker build -t mysql:5.6 . (--no-cache=true when dont want to use existing cache)

# list existing images
> docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
mysql                   5.6                 36c3291f3a2d        8 seconds ago       324.2 MB
...

# run 
> docker run --name mysql mysql:5.6 -e MYSQL_ROOT_PASSWORD=secret
> docker run --name mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=test -e MYSQL_USER=foo -e MYSQL_PASSWORD=secret -d mysql:5.6
ff01b4bea17e4370b64c3096eb78fd7edd8146f3fc39605506b823831a698d1c

# list process
> docker ps                                                                                                                 
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
ff01b4bea17e        mysql:5.6               "/entrypoint.sh mysql"   4 seconds ago       Up 4 seconds        3306/tcp                 mysql

# log
> docker logs mysql
...

# connect to container
> docker exec -it mysql bash
root%fdafa:/#

# run client container
> docker run --link mysql:mysql -it --rm mysql:5.6 bash
root@b801dd755415:/#

# conn from client container
> docker run --link mysql:mysql -it --rm mysql:5.6 bash
root@b801dd755415:/#
root@b801dd755415:/# mysql -u foo -psecret -h $MYSQL_PORT_3306_TCP_ADDR test
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.29 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```
