# docker-secure-registry


    \newline
Docker Secure Private Registry Using openssl


    \newline
##########STEP 1##########


    \newline
Clone this repo



    \newline

##########STEP 2##########


    \newline
edit docker-compose.yaml


    \newline
  change your-web.com to any name you want


    \newline
edit nginx/registry.conf


    \newline
  change your-web.com to any name you want


    \newline

##########STEP 3##########


    \newline
Create Authentication


    \newline
  cd docker-registry/nginx


    \newline
  htpasswd -c registry.password <username>


    \newline
Enter password you like.


    \newline

##########STEP 4##########


    \newline
edit /etc/hosts


    \newline
  In last of file add this line


    \newline
  <your-server-ip> <your-web.com>


    \newline

##########STEP 5##########


    \newline
edit /etc/ssl/openssl.conf and add following lines


    \newline
  subjectAltName= @alt_names


    \newline
  
  [ alt_names ]


    \newline
  IP.1 = Your-server-ip


    \newline
  DNS.1 = your-web.com


    \newline

##########STEP 6##########


    \newline
Generating SSL Certificate


    \newline
  cd docker-secure-registry


    \newline
  cd nginx


    \newline
  openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myregistry.key -out myregistry.cert


    \newline
 You'll be prompted some parameters. Just Press Enter for all except "Common Name (e.g. server FQDN or YOUR name) []:"
 Type <your-server-ip> in it and press enter.


    \newline
 
##########STEP 7##########


    \newline
Generating CA Certificate. Goto nginx folder in repo and type following command


    \newline
  openssl req -new -key myregistry.key -out myregistry.csr


    \newline
  openssl genrsa -out ca.key 2048


    \newline
  openssl req -new -x509 -key ca.key -out ca.crt


    \newline
  openssl x509 -req -in myregistry.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out myregistry.crt


    \newline
  
You'll be prompted some parameters. Just Press Enter for all except "Common Name (e.g. server FQDN or YOUR name) []:"
Type <your-server-ip> in it and press enter.
   

    \newline

The generated ca.crt file is the CA certificate and will be used by clients and this server too.


    \newline

##########STEP 8##########


    \newline
Copy myregistry.crt file from nginx folder to /etc/ssl/certs/ and type following commands


    \newline
  update-ca-certificates


    \newline
  systemctl daemon-reload


    \newline
  systemctl restart docker


    \newline

##########STEP 9##########


    \newline
Creating Docker Registry


    \newline
  goto docker-secure-registry folder and type following command


    \newline
  docker-compose -f docker-compose.yaml up -d


    \newline
  
##########STEP 10##########


    \newline
Verify if it works, type following commands


    \newline
  docker login <your-web.com>


    \newline
    
   Enter your username and password you set before.It should be succeeded
  

    \newline

  docker pull hello-world


    \newline
  docker tag hello-world <your-web.com>/hello-world


    \newline
  docker push <your-web.com>/hello-world


    \newline
  docker pull <your-web.com>/hello-world


    \newline

For client side give myregistry.crt file to client and ask them to do step 8
  


