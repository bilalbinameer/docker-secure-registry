# docker-secure-registry


&nbsp;
Docker Secure Private Registry Using openssl


&nbsp;
##########STEP 1##########


&nbsp;
Clone this repo



&nbsp;

##########STEP 2##########


&nbsp;
edit docker-compose.yaml


&nbsp;
  change your-web.com to any name you want


&nbsp;
edit nginx/registry.conf


&nbsp;
  change your-web.com to any name you want


&nbsp;

##########STEP 3##########


&nbsp;
Create Authentication


&nbsp;
  cd docker-registry/nginx


&nbsp;
  htpasswd -c registry.password <username>


&nbsp;
Enter password you like.


&nbsp;

##########STEP 4##########


&nbsp;
edit /etc/hosts


&nbsp;
  In last of file add this line


&nbsp;
  <your-server-ip> <your-web.com>


&nbsp;

##########STEP 5##########


&nbsp;
edit /etc/ssl/openssl.conf and add following lines


&nbsp;
  subjectAltName= @alt_names


&nbsp;
  
  [ alt_names ]


&nbsp;
  IP.1 = Your-server-ip


&nbsp;
  DNS.1 = your-web.com


&nbsp;

##########STEP 6##########


&nbsp;
Generating SSL Certificate


&nbsp;
  cd docker-secure-registry


&nbsp;
  cd nginx


&nbsp;
  openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myregistry.key -out myregistry.cert


&nbsp;
 You'll be prompted some parameters. Just Press Enter for all except "Common Name (e.g. server FQDN or YOUR name) []:"
 Type <your-server-ip> in it and press enter.


&nbsp;
 
##########STEP 7##########


&nbsp;
Generating CA Certificate. Goto nginx folder in repo and type following command


&nbsp;
  openssl req -new -key myregistry.key -out myregistry.csr


&nbsp;
  openssl genrsa -out ca.key 2048


&nbsp;
  openssl req -new -x509 -key ca.key -out ca.crt


&nbsp;
  openssl x509 -req -in myregistry.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out myregistry.crt


&nbsp;
  
You'll be prompted some parameters. Just Press Enter for all except "Common Name (e.g. server FQDN or YOUR name) []:"
Type <your-server-ip> in it and press enter.
   

&nbsp;

The generated ca.crt file is the CA certificate and will be used by clients and this server too.


&nbsp;

##########STEP 8##########


&nbsp;
Copy myregistry.crt file from nginx folder to /etc/ssl/certs/ and type following commands


&nbsp;
  update-ca-certificates


&nbsp;
  systemctl daemon-reload


&nbsp;
  systemctl restart docker


&nbsp;

##########STEP 9##########


&nbsp;
Creating Docker Registry


&nbsp;
  goto docker-secure-registry folder and type following command


&nbsp;
  docker-compose -f docker-compose.yaml up -d


&nbsp;
  
##########STEP 10##########


&nbsp;
Verify if it works, type following commands


&nbsp;
  docker login <your-web.com>


&nbsp;
    
   Enter your username and password you set before.It should be succeeded
  

&nbsp;

  docker pull hello-world


&nbsp;
  docker tag hello-world <your-web.com>/hello-world


&nbsp;
  docker push <your-web.com>/hello-world


&nbsp;
  docker pull <your-web.com>/hello-world


&nbsp;

For client side give myregistry.crt file to client and ask them to do step 8
  


