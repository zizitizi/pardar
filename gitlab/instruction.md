# this config works for me perfectly

We will start work on the installation by creating a dedicated directory in which we will store data and Gitlab configuration.

> mkdir gitlab


For convenience, we can also set an environment variable that will contain the path to our Gitlab directory: but i do simple way

> export GITLAB_HOME=$(pwd)/gitlab


In the next step, we create the docker-compose.yml file with the following content:

I'm using Gitlab 7.5.3 omnibus 5.2.1 ci on Debian Squeeze. To change the default port for nginx add the _gitlab_port_ to /etc/gitlab/gitlab.rb:

My /etc/gitlab/gitlab.rb:

external_url = 'gitlab.example.org'
gitlab_rails['gitlab_port'] = 12345
After changing the port, you have to call gitlab-ctl reconfigure and gitlab-ctl restart in your command line.

      
                        # docker-compose.yml
                  version: '3.6'
                  services:
                    web:
                      image: 'gitlab/gitlab-ce:latest'
                      restart: always
                      hostname: 'gitlab.example.com'
                      environment:
                        GITLAB_OMNIBUS_CONFIG: |
                          external_url 'http://gitlab.example.com:8929'
                          gitlab_rails['gitlab_shell_ssh_port'] = 2224
                      ports:
                        - '8929:8929'
                        - '2224:22'
                      volumes:
                        - '$GITLAB_HOME/config:/etc/gitlab'
                        - '$GITLAB_HOME/logs:/var/log/gitlab'
                        - '$GITLAB_HOME/data:/var/opt/gitlab'
                      shm_size: '256m'
                                       


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

--------------------------

 add specifeid user and gruop to your repo in admin area first


git init: start
git add .: add to coomit stage
git commit -m "Initial commit": commit change
git remote add origin <remote repository URL>: connect to repo
git push -u origin master: send change to repo use -uf to force


