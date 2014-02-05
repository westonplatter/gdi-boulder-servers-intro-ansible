ROUGH DRAFT STEPS TO REPRODUCE IN ANSIBLE

DO - digital ocean. hosting provider for student servers.


0. locally create SSH public/private key

    use the github tutorial to creete their own SSH key (use current one if they already have an SSH key)

1. ssh into Digital Ocean (DO) using root and password

    replace ip_address with actual ip
  
        ssh root@ip_address
    

2. create `deploy` user
  
    create user

        useradd deploy -d /home/deploy -m -s /bin/bash

    set password
  
        passwd deploy
        # enter new password after prompt
  

3. give `deploy` user sudo access

    add user to sudo group
        
        usermod -aG sudo deploy
  
  
4. copy over id_rsa.pub to authorized_keys
  
    make the .ssh directory
  
        cd /home/deploy
        mkdir .ssh
  
  
    create the authorized_keys file
  
        cd .ssh
        touch authorized_keys
  
  
    copy over the user's id_rsa.pub file contents into authorized_keys
  
        vim authorized_keys
  
        chown -R deploy:deploy /home/deploy/.ssh
  
        chmod -R 700 /home/deploy/.ssh


4. test `deploy` ssh authorized

    ssh into box 
        
        ssh deploy@ip_address
  
  
5. disable root ssh access by editing the /etc/ssh/sshd_config file
  
    use vim to edit file
    
        sudo vim /etc/ssh/sshd_config
  
        # before
        #   PermitRootLogin yes
        #
        # after
        #    PermitRootLogin no

  
6. restart the server's SSH demonized process

    use the service command 
  
        sudo service ssh restart
  

7. install nginx (installs version 1.1.19)
  
    first, update the list of installable pacakges
  
        sudo apt-get update
  
  
    install nginx
  
        sudo apt-get install nginx
  
  
8. test that it's working

    get the nginx status
  
        sudo service nginx status
  
  
    Go to the server's IP address, http://ip_address. You should see "NGINX is working!"
  
    Also tail the access logs
  
  
        cd /var/log
        ls # should show that nginx folder is present
  
        cd /var/log/nginx
        tail -f access.log
  

9. add `html_site` configuration file to `/var/nginx/sites-enabled`

    use vim to create 
        
        sudo vim /etc/nginx/sites-enabled/html_site
  
  
    the file 
      
        # html_site configuration 
        
        server {
          listen 7001;
          server_name html_site;
          root /var/www/html_site;
          index index.html index.htm;
        }
  


10. create the html folder 

    create `/var/www` folder
  
        sudo mkdir /var/www
  
  
    change user and group to `deploy`
  
        sudo chown -R deploy:deploy /var/www
        sudo chmod -R 0644 /var/www
  
  
    create `html_site` folder
  
        cd /var/www
        mkdir html_site
  

11. manually copy over HTML files into `/var/www/html_site` folder

    manually copy and paste HTML between computer and DO server (we'll keep the files __really__ simple)
  
  
12. restart nginx to pick up the new html site
  
    use service command again
      
        sudo service nginx restart
  
    Go to ip:7000

13. install nodejs from the chris lea ppa
  
    update apt-get and install nodejs build dependencies
  
        sudo apt-get update
        sudo apt-get install -y python-software-properties python g++ make
    
    
    add a non-default apt-get ppa
  
        sudo add-apt-repository ppa:chris-lea/node.js
        sudo apt-get update
  
  
    install nodejs
  
        sudo apt-get install nodejs
  

    test that you have node 0.10.x
    
        node -v
  

14. setup nginx reverse proxy
  
    create a reverse proxy nginx server 
  
        cd /etc/nginx/sites-enabled
        sudo vim touch nodejs_uploader
  
  
    nodejs_uploader nginx conf 
  
        server {
            listen 9000;
            location / { 
                proxy_pass http://127.0.0.1:7020;
            } 
        }
  
  
    test it out
  
        sudo service nginx restart
      
  
    go to ip:9000, and you should see a NGINX 500 Bad Gateway page.

15. install git

    use apt-get
      
        sudo apt-get update
        sudo apt-get install git
  
  
16. clone westonplatter/gdi-boulder-servers-intro-node-uploader
  
    git clone the repo to the server
  
        cd /var/www
        git clone git@github.com:westonplatter/gdi-boulder-servers-intro-node-uploader.git nodejs_uploader
  
    
17. install nodejs dependencies

    install nodejs dependencies via npm
  
        cd /var/www/nodejs_uploader
        npm install
  
  
    test that it works
  
        node server.js
  
  
    go to ip:9000, and you should see upload page

18. keep the app running via forever

    install forever with sudo since `node_modules` are installed in `/usr/lib` by default
  
        sudo node install -g forever

  
    see list of forever processes
  
        forever list
  
  
    start the app with forever
  
        forever start node server.js
  
  
    now you can exit out of the SSH connection and the app still works

19. modify nginx to enable larger file uploads

    open `/etc/nginx/nginx.conf` for editing
    
        sudo vim /etc/nginx/nginx.conf
    
    add this configuration to allow uploads up to 5 megs
        
        client_max_body_size 5m;

20. test upload ability
    
    upload 4mb file


