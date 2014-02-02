ROUGH DRAFT STEPS TO REPRODUCE IN ANSIBLE

1. ssh with root and password

	```sh
	ssh root@ip_address
	```

2. create `deploy` user

  commands - http://linux.about.com/od/commands/l/blcmdl8_useradd.htm
  
  create
  
	```sh
	useradd deploy -d /home/deploy -m -s /bin/bash
	```
  set password
  
	```sh
	passwd deploy
	# enter new password after prompt
	```

3. give `deploy` user sudo access

	```sh
	usermod -aG sudo deploy
	```
	
4. copy over id_rsa.pub to authorized_keys
	
	make the .ssh directory
	```sh
	cd /home/deploy
	mkdir .ssh
	```
	
	create the authorized_keys file
	```sh
	cd .ssh
	touch authorized_keys
	```
	
	copy over the user's id_rsa.pub file contents into authorized_keys
	```sh
	vim authorized_keys
	
	chown -R deploy:deploy /home/deploy/.ssh
	
	chmod -R 700 /home/deploy/.ssh
	```

4. test `deploy` ssh authorized

	```ssh
	ssh deploy@ip_address
	```
	
5. disable root ssh access by editing the /etc/ssh/sshd_config file
	
	```sh
	sudo vim /etc/ssh/sshd_config
	
	# before
	#   PermitRootLogin yes
	#
	# after
	#    PermitRootLogin no
	```
	
6. restart the server's SSH demonized process

	```sh
	sudo service ssh restart
	```

7. install nginx (installs version 1.1.19)
  
	first, update the list of installable pacakges
	```sh
	sudo apt-get update
	```
	
	install nginx
	```sh
	sudo apt-get install nginx
	```
	
8. test that it's working

	```sh
	sudo service nginx status
	```
	
	Go to the server's IP address, http://ip_address. You should see "NGINX is working!"
	
	Also tail the access logs
	
	```sh
	cd /var/log
	ls # should show that nginx folder is present
	
	cd /var/log/nginx
	tail -f access.log
	```

9. add `html_site` configuration file to `/var/nginx/sites-enabled`

	```sh
	sudo vim /etc/nginx/sites-enabled
	```
	
	```
	# html_site configuration 
	
	server {
		listen 7001;
		server_name html_site;
		root /var/www/html_site;
		index index.html index.htm;
	}
	```


10. create the html folder 

	create `/var/www` folder
	```sh
	sudo mkdir /var/www
	```
	
	change user and group to `deploy`
	```sh
	sudo chown -R deploy:deploy /var/www
	sudo chmod -R 0644 /var/www
	```
	
	create `html_site` folder
	```sh
	cd /var/www
	mkdir html_site
	```

11. scp files up to the `/var/www/html_site` folder

	```sh
	scp -r ./html_site/* deploy@ip:/var/www/html_site
	```
	
12. restart nginx to pick up the new html site
	
	```sh 
	sudo service nginx restart
	```
	Go to ip:7000
 
