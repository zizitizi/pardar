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

##  ssh to container to avoid user name and password asking in pull and push and...


make sure in docker compose to install open ssh server and permit root login yes:

Update the package list: Run the command apt-get update to update the package list inside the container.

Install SSH: Run the command apt-get install -y openssh-server to install the SSH server package.

Configure SSH: Edit the SSH server configuration file /etc/ssh/sshd_config to configure the SSH server. For example, you can set the PermitRootLogin option to yes to allow root login via SSH.

Start the SSH service: Run the command service ssh start or /etc/init.d/ssh start to start the SSH service inside the container.

Generate a new SSH key pair by running the command ssh-keygen without specifying a filename. Press Enter to accept the default file location (/home/zizi/.ssh/id_rsa) and an empty passphrase, or provide a passphrase if desired.

After generating the SSH key pair, you should have two files: id_rsa (private key) and id_rsa.pub (public key) in the /home/zizi/.ssh/ directory.

To copy the public key to the container, use the following command: ssh-copy-id -i ~/.ssh/id_rsa.pub root@172.23.0.2.


git remote set-url origin git@<gitlab-server>:<username>/<project>.git.






--------------------------

 add specifeid user and gruop to your repo in admin area first


git init: start
git add .: add to coomit stage
git commit -m "Initial commit": commit change
git remote add origin <remote repository URL>: connect to repo  - url that land in clone with ssh section
git push -u origin master: send change to repo use -uf to force


git remote set-url origin ssh://git@192.168.44.136:2224/pardar/pardar.git


## GitLab SSH key configuration steps
The steps to follow to configure GitLab SSH keys for secure Git connections are as follows:

Create an SSH key pair on your personal computer
Copy the value of the public SSH key
Log into GitLab and navigate to your account’s Preferences tab
Create a new GitLab SSH key
Paste the public key in as the value and set an expiration date
Copy the SSH URL of the GitLab repo you wish to clone
Issue a git clone command with the SSH URL



related usefull url:

https://virgool.io/@aminkt/%DA%86%DA%AF%D9%88%D9%86%D9%87-%D9%BE%D8%B1%D9%88%DA%98%D9%87-vuejs-%D8%AE%D9%88%D8%AF-%D8%B1%D8%A7-%D8%A8%D8%A7-%D8%A7%D8%B3%D8%AA%D9%81%D8%A7%D8%AF%D9%87-%D8%A7%D8%B2-ssh-%D9%88-gitlab-%D8%A8%D8%B1-%D8%B1%D9%88%DB%8C-%D8%B3%D8%B1%D9%88%D8%B1-deploy-%DA%A9%D9%86%DB%8C%D9%85-f0vr9ancpbhh
https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/How-to-configure-GitLab-SSH-keys-for-secure-Git-connections


### gitlab first time login pass :


if you can not access :

docker exec -it gitlab-ce grep 'Password:' /etc/gitlab/initial_root_password


then go into gitlab container with bash then:

gitlab-rake "gitlab:password:reset"


then user root change password with 8 character. then reset container.




### docker login

Here are the steps to connect your GitLab project to Docker Hub and set the environment variables:

Step 1: Connect your GitLab project to Docker Hub:

Go to your GitLab project page.
Click on "Settings" in the left sidebar.
Select "CI/CD" from the dropdown menu.
Scroll down to the "Variables" section.
Click on "Add Variable".
Set the key as DOCKER_USERNAME and the value as your Docker Hub username.
Click on "Add Variable" again.
Set the key as DOCKER_PASSWORD and the value as your Docker Hub password or access token.
Click on "Save changes".
Step 3: Set the environment variables:

Open your .gitlab-ci.yml file.
Replace $DOCKER_USERNAME and $DOCKER_PASSWORD with the actual environment variable names, e.g., ${DOCKER_USERNAME} and ${DOCKER_PASSWORD}.
That's it! Now your GitLab project is connected to Docker Hub, and the environment variables are set for your pipeline to authenticate and push the image to Docker Hub.

 script:
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin






