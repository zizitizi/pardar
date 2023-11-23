# this config works for me perfectly

We will start work on the installation by creating a dedicated directory in which we will store data and Gitlab configuration.

> mkdir gitlab


For convenience, we will also set an environment variable that will contain the path to our Gitlab directory:

> export GITLAB_HOME=$(pwd)/gitlab


In the next step, we create the docker-compose.yml file with the following content:

      
      # docker-compose.yml
version: '3.7'
services:
  web:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'localhost'
    container_name: gitlab-ce
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.44.136:8080'
        nginx['listen_port'] = 8080
        nginx['listen_https'] = false

    ports:
      - '8080:80'
      - '8443:443'
    volumes:
      - '/home/zizi/gitlab/config:/etc/gitlab'
      - '/home/zizi/gitlab/logs:/var/log/gitlab'
      - '/home/zizi/gitlab/data:/var/opt/gitlab'
    networks:
      - gitlab


  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    container_name: gitlab-runner
    restart: always
    depends_on:
      - web
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - '/home/zizi/gitlab/gitlab-runner:/etc/gitlab-runner'
    networks:
      - gitlab

networks:
  gitlab:
    name: gitlab-network
~
   


This configuration defines what containers we want to run. In our case, it will be the GitLab service with one GitLab runner (a separate module for running CI / CD tasks).


Gitlab installation:


Containers are started using the command:

> docker-compose up -d


Once launched, Docker will download GitLab and GitLab Runner images from the servers. 


To log in to GitLab for the first time, you need a temporary password, which is generated automatically during installation. We get the password using the command:

> docker exec -it gitlab-ce grep 'Password:' /etc/gitlab/initial_root_password


GitLab launching:


Our GitLab is available at: http://localhost:8080. After going to this address, the following screen should appear:

This is a basic installation process, and you can customize GitLab CI further according to your requirements. Note that considerations such as security and backup are crucial and depend on your deployment environment and needs. Ensure regular backups of GitLab CI data to prevent data loss.



if you can not access :

docker exec -it gitlab-ce grep 'Password:' /etc/gitlab/initial_root_password


then go into gitlab container with bash then:

gitlab-rake "gitlab:password:reset"


then user root change password with 8 character. then reset container.


