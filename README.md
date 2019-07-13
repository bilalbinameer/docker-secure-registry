# docker-secure-registry
Docker Secure Private Registry Using openssl
##########STEP 1##########
Clone this repo

##########STEP 2##########
edit docker-compose.yaml
  change your-web.com to any name you want
edit nginx/registry.conf
  change your-web.com to any name you want

##########STEP 3##########
Create Authentication
  cd docker-registry/nginx
  htpasswd -c registry.password <username>
Enter password you like.

##########STEP 4##########
edit /etc/hosts
  In last of file add this line
  <your-server-ip> <your-web.com>

##########STEP 5##########
edit /etc/ssl/openssl.conf and add following lines
  subjectAltName= @alt_names
  
  [ alt_names ]
  IP.1 = Your-server-ip
  DNS.1 = your-web.com

##########STEP 6##########
Generating SSL Certificate
  cd docker-secure-registry
  cd nginx
  openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myregistry.key -out myregistry.cert
 You'll be prompted some parameters. Just Press Enter for all except "Common Name (e.g. server FQDN or YOUR name) []:"
 Type <your-server-ip> in it and press enter.
 
##########STEP 7##########
Generating CA Certificate. Goto nginx folder in repo and type following command
  openssl req -new -key myregistry.key -out myregistry.csr
  openssl genrsa -out ca.key 2048
  openssl req -new -x509 -key ca.key -out ca.crt
  openssl x509 -req -in myregistry.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out myregistry.crt
  
You'll be prompted some parameters. Just Press Enter for all except "Common Name (e.g. server FQDN or YOUR name) []:"
Type <your-server-ip> in it and press enter.
The generated ca.crt file is the CA certificate and will be used by clients and this server too.

##########STEP 8##########
Copy myregistry.crt file from nginx folder to /etc/ssl/certs/ and type following commands
  update-ca-certificates
  systemctl daemon-reload
  systemctl restart docker

##########STEP 9##########
Creating Docker Registry
  goto docker-secure-registry folder and type following command
  docker-compose -f docker-compose.yaml up -d
  
##########STEP 10##########
Verify if it works, type following commands
  docker login <your-web.com>
    Enter your username and password you set before.It should be succeeded
  docker pull hello-world
  docker tag hello-world <your-web.com>/hello-world
  docker push <your-web.com>/hello-world
  docker pull <your-web.com>/hello-world

For client side give myregistry.crt file to client and ask them to do step 8.
  


