# pardar
pardar issue


use below diagram

![image](https://github.com/zizitizi/pardar/assets/123273835/853a8833-9bbf-43db-872b-5fb1d36d2783)


all error and bugs report in trelo


# my scratch pipeline:


from production to test backup and test process develop


1- take backup and zip from volume folder:

from folder:

      crontab -e

                        0 5 * * 1 tar -zcf /var/backups/home.tgz /home/  - get backup from /home and make tar file in directory /var/backups/home.tgz

from container:

                        #!/bin/bash
                        
                        #this script take an iage of all running container and save it in .tar in home directory and remove old one form previouse 60 minute.
                        
                        #*********please give this file +x permission to execute backup********************
                        
                        #*********please copy this file in /etc/cron.hourly/ to perform auotomated action backup per hour********************
                        
                        # get all running docker container names
                        
                        containers=$(sudo docker ps | awk '{if(NR>1) print $NF}')
                        
                        #get host names
                        
                        host=$(hostname)
                        
                        #repousername="zeintiz"
                        
                        #reponame="container-backup"
                        
                        tagname=`date +%Y%m%d-%H%M%S`
                        
                        #OUTPUT=/home/zizi/
                        
                        for container in $containers
                        do
                                echo "container name is:" $container
                                CONNAME=${container}:${tagname}
                                echo "backup saved image name is: backup-"$CONNAME
                                docker commit ${container} backup-${CONNAME}
                                docker save backup-${CONNAME} > ~/backup-${CONNAME}.tar
                                if [ $? -eq 0 ]
                                then
                                         find ~ -name "backup-${container}*.tar" -type f -mmin +60 -exec rm -rf {} \;
                                fi
                        done
                        





2- write specified docker compose file to test and production environmet:

                        
                        version: '3.1'
                        services:
                          web:
                            image: zeintiz/pardar:pardarweb20231116
                            container_name: proj-web-1
                            hostname: odoo
                            networks:
                              - proj-net
                            depends_on:
                              - db
                            ports:
                              - "8060:8069"
                            volumes:
                              - odoo-web-data:/var/lib/odoo
                              - /home/zizi/proj/config:/etc/odoo
                              - /home/zizi/proj/addons:/mnt/extra-addons
                              - /home/zizi/proj/logs:/var/log/odoo
                            env_file: .env
                          db:
                            image: zeintiz/pardar:pardardb20231116
                            container_name: proj-db-1
                            hostname: postgres
                            networks:
                              - proj-net
                            env_file: .env
                            volumes:
                              - odoo-db-data:/var/lib/postgresql/data/pgdata
                        volumes:
                            odoo-web-data:
                            odoo-db-data:
                        networks:
                            proj-net:
                        


2- send backup to test environmet


scp :

remote to local ad specified folder:

                        scp -r zizi@192.168.44.142:/home/zizi/Desktop/Newfolder/ /home/zizi/



3- unzip each one to specefied path


gunzip file1.gz or gzip -d file1.gz - uncompressing


4- commit webapp and db container in production - tag it - push it to docker hub


5- run and pull it in test environmet


4- change and develope apps



5- test  it if true or without errorr and bug. debug it








5- pull and run docker image in test environmet


6- in test docker compose up


7- test app is succefully running


from test to production

8- develop new feature if successful


9- if successful 









# works

install gitlab to write pipe line.



