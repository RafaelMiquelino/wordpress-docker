# Wordpress docker
This repository is ready to run Wordpress behind a Traefik reverse proxy.
## Setup Instructions
### Install Docker
1. First, in order to ensure the downloads are valid, add the GPG key for the official Docker repository to your system:  
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
2. Add the Docker repository to APT sources:  
`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
3. Next, update the package database with the Docker packages from the newly added repo:  
`sudo apt-get update`
4. Make sure you are about to install from the Docker repo instead of the default Ubuntu 16.04 repo:  
`apt-cache policy docker-ce`  
 You should see output similar to the follow:  
```
docker-ce:
    Candidate: 18.06.1~ce~3-0~ubuntu
    Version table:
        18.06.1~ce~3-0~ubuntu 500
            500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```
5. Finally, install Docker:  
`sudo apt-get install -y docker-ce`
6. Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it's running:  
`sudo systemctl status docker`
7. If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group:  
`sudo usermod -aG docker ${USER}`
8. To apply the new group membership, log out of the server and back in.
9. Afterwards, you can confirm that your user is now added to the docker group by typing:  
`id -nG`  

- For more info, visit: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04
### Install Docker Compose
1. We'll check the current release and if necessary, update it in the command below:  
`ssudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
2. Next we'll set the permissions:  
`sudo chmod +x /usr/local/bin/docker-compose`
3. Then we'll verify that the installation was successful by checking the version:  
`docker-compose -v`

- For more info, visit: https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-16-04
### Install the repository into the VPS
1. Clone the repository.
1. Step into the just created folder 

### Configure environment variables
1. Create a .env file with the required information
2. A sample .env file is provided to show which information must me provided 

### Backup wp-content:  
`docker run --rm --volumes-from cms -v $(pwd)/wp-content:/backup ubuntu tar cvf /backup/wp-content.tar /var/www/html/wp-content`  
### Restore wp-content:  
`docker run --volumes-from cms -v $(pwd)/wp-content:/backup ubuntu bash -c "cd /var && tar xvf /backup/wp-content.tar --strip 1"`  
### Backup DB  
`docker exec db sh -c 'exec mysqldump -uroot -p"$MYSQL_ROOT_PASSWORD" cms' | gzip -9 > ./cms_db.sql.gz`  
### Restore DB  
`gzip -d < cms_db.sql.gz | docker exec -i db sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD" cms'`
