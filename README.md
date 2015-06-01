# Apache config

----> Instalar

	sudo apt-get install apache2

----> Habilitar
	
	cd /etc/apache2/mods-available

	sudo a2enmod proxy
	sudo a2enmod proxy_http
	sudo a2enmod proxy_wstunnel
	sudo a2enmod ssl	
	sudo a2enmod headers

----> Editar apache2

	sudo gedit /etc/apache2/apache2.conf

Adicionar

	ServerName teste.com.br

----> Gerar ssl

	sudo mkdir /etc/ssl

	sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/teste.key -out /etc/ssl/teste.crt
	

----> Editar proxy
	
	sudo gedit /etc/apache2/mods-enabled/proxy.conf

Adicionar

	<VirtualHost *:80>
		ProxyRequests off
		ProxyPreserveHost on
		ServerAdmin teste@gmail.com
		ServerName teste.com.br

		#ErrorLog "/var/log/apache2/teste-error_log"
		#CustomLog "/var/log/apache2/teste-access_log" common

		ProxyPass /<context>/<webSocketNameMap> ws://<ip:porta_server_app>/<context>/<webSocketNameMap>
		ProxyPassReverse /<context>/<webSocketNameMap> ws://<ip:porta_server_app>/<context>/<webSocketNameMap>

		ProxyPass /<context> http://<ip:porta_server_app>/<context>
		ProxyPassReverse /<context> http://<ip:porta_server_app>/<context>

	</VirtualHost>

	<VirtualHost *:443>
		ProxyRequests off
		ProxyPreserveHost on
		ServerAdmin teste@gmail.com
		ServerName teste.com.br
		
		#ErrorLog "/var/log/apache2/teste-error_log"
		#CustomLog "/var/log/apache2/teste-access_log" common

		SSLEngine on
		SSLProxyEngine on
		SSLProxyVerify none 
		SSLProxyCheckPeerCN off
		SSLProxyCheckPeerName off
		SSLProxyCheckPeerExpire off
		
		RequestHeader set Front-End-Https on

		SSLCertificateFile /etc/ssl/teste.crt
		SSLCertificateKeyFile /etc/ssl/teste.key

		#Alias / "/etc/httpd/htdocs/sgr-dev/"

		<Proxy *>
		 Order deny,allow
		 Allow from all
		</Proxy>

		ProxyPass /<context>/<webSocketNameMap> wss://<ip:porta_server_app>/<context>/<webSocketNameMap>
		ProxyPassReverse /<context>/<webSocketNameMap> wss://<ip:porta_server_app>/<context>/<webSocketNameMap>

		ProxyPass /<context> http://<ip:porta_server_app>/<context>
		ProxyPassReverse /<context> http://<ip:porta_server_app>/<context>

	</VirtualHost>

----> Editar hosts

	sudo gedit etc/hosts

Adicionar

	<ip_server_apache> teste.com.br

----> Fim
	
	sudo service apache2 restart
