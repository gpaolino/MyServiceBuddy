# MERNapp
Custom service diary app developed with MERN stack technology


# DEV TUTORIAL:
https://www.youtube.com/watch?v=98BzS5Oz5E4&list=PL4cUxeGkcC9iJ_KkrkBZWZRHVwnzLIoUE&index=1

TOOLS, FRAMEWORK & DEPENDENCIES:
- Visual Studio Code (https://code.visualstudio.com/download)
- Nodejs (https://nodejs.org/en/)
- Express Package (npm install express)
- Init package.json (npm init -y)
- Install nodemon globally (npm install -g nodemon)
- Install dotenv (npm install dotenv)
- Postman (https://www.postman.com/downloads/)
- MongoDB CE on premise (https://www.mongodb.com/try/download/community)
- Mongoose (npm install mongoose)
- React Fronted (npx create-react-app frontend)
- Upgrade tar (npm install tar@6 -g)
- React Router Dom (npm install react-router-dom)
- Google Material Symbols (https://developers.google.com/fonts/docs/material_icons)
- Google Material Symbols offline (npm install material-symbols@latest)
- Date-fns (npm install date-fns)
- OpenSSL (https://thesecmaster.com/procedure-to-install-openssl-on-the-windows-platform/)

AUDIT PACKAGE:
- Check inside backend folder (npm audit)
- Fix vulnerabilities (npm audit fix --force)
- Fix npm audit error for "react-scripts" (https://github.com/facebook/create-react-app/issues/11174)
- Check inside frontend folder (npm audit --production)

ENABLE HTTPS ON LOCALHOST:
- (https://javascript.plainenglish.io/enable-https-for-localhost-during-local-development-in-node-js-96204453d72b)
- (https://stackoverflow.com/questions/21397809/create-a-trusted-self-signed-ssl-cert-for-localhost-for-use-with-express-node)
- openssl req -x509 -nodes -days 999 -newkey rsa:2048 -keyout cert.key -out cert.pem -config req.cnf -sha256
  Edit: before executing the command edit req.cnf and set CN and DNS.1 equal to the host name instead of localhost (ex. myservername, ecc...)

LOCALHOST:
```
[req]
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no
[req_distinguished_name]
C = IT
ST = State
L = Location
O = Organization Name
OU = Organizational Unit 
CN = www.localhost.com
[v3_req]
keyUsage = critical, digitalSignature, keyAgreement
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1 = www.localhost.com
DNS.2 = localhost.com
DNS.3 = localhost
```

HOME SERVER:
```
[req]
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no
[req_distinguished_name]
C = IT
ST = State
L = Location
O = Organization Name
OU = Organizational Unit 
CN = serverubuntu0
[v3_req]
keyUsage = critical, digitalSignature, keyAgreement
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
DNS.1 = serverubuntu0
DNS.2 = localhost.com
DNS.3 = localhost
```

ENABLE HTTPS ON YOUR DOMAIN:
- (https://letsencrypt.org/it/getting-started/)
- (https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)
- (https://r.je/guide-lets-encrypt-certificate-for-local-development)

TO START DB SEREVR (DEV):
- Check MongoDB Server (MongoDB) service is up and running

TO START BACKEND NODE SERVER FROM VSCODE (DEV):
- cd .\backend\
- npm run dev

TO START FRONTEND REACT APP FROM VSCODE (DEV):
- cd .\frontend\
- npm start


# DEPLOY ON AWS (or ON A PERSONAL HOME SERVER LINUX):
How To Build And Deploy A MERN Stack Application On AWS?
(https://www.workfall.com/learning/blog/how-to-build-and-deploy-a-mern-stack-application-on-aws/)
(https://www.youtube.com/watch?v=FanoTGjkxhQ)

Hands-on
Steps:

- Create a new Ubuntu server instance on EC2
- Connect to Ubuntu server via SSH
- Setup web server with Node.JS +MongoDB+ NGINX 
- Deploy backend API
- Deploy Front end API using React 
- Configure NGINX to serve the Node.JS API and React front-end
- Test your MERN stack application running on AWS


# Create a new Ubuntu server instance on EC2:
Sign into the AWS console, go to the EC2 section
Follow the steps to launch an Ubuntu AMI EC2 instance
Configure the security group to allow HTTP traffic, click review, and launch
Download the key pair, click launch instances then scroll to the bottom and click on View instances to see the instance in running state. 


# Connect to Ubuntu server via SSH:
Using windows, using PuttyGen to convert. pem keypair file to private file (.ppk)
Use Putty to connect to the EC2 instance via SSH


While connected to the new AWS EC2 instance in the terminal window:

Clone the Node.JS + MongoDB API project with the following command: 
- sudo git clone https://github.com/gpaolino/MyServiceBuddy.git

Run the bash script (script.sh) file present in the home directory of the repo (Updated version, execute step by step)
It executes a script to automatically set up and configure a production-ready MERN Stack web server on Ubuntu that includes Node.JS, MongoDB, PM2, NGINX, and UFW.


# Deploy backend API:

Follow these steps to set up the Node.JS API on the server and configure NGINX to enable access to it.

Clone the Node.JS + MongoDB API project into the /opt/backend directory with the following command:
- sudo git clone https://github.com/gpaolino/MyServiceBuddy.git /opt/

Navigate into the back-end directory and install all required npm packages with the following command:
- cd /opt/backend && sudo npm install

Create the .env files for production configurations:
- sudo vi /opt/backend/.env

If your app uses https don't forget to create ceritifacte under the backend/security/ folder:
- sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout cert.key -out cert.pem -config req.cnf -sha256

Start the API using the PM2 process manager with the following command:
- sudo pm2 start server.js

The API is now running on Node.JS under the PM2 process manager and listening on port 4000.

# Enable PM2 to Autostart server.js App at System Boot:
- sudo pm2 startup
- sudo pm2 startup systems
- sudo systemctl status pm2-root.service
- cd /opt/backend/
- sudo pm2 start server.js
- sudo pm2 save
- sudo pm2 ls
- (to manually resurrect processes use: sudo pm2 resurrect) 


# Deploy Front end API using React:

Clone the React + Redux project into the /opt/frontend directory with the following command:
- sudo git clone https://github.com/gpaolino/MyServiceBuddy.git /opt/

Navigate into the front-end directory and install all required npm packages with the following command:
- cd /opt/frontend && sudo npm install

Build the front-end app with the following command:
- sudo npm run build

The React app is now built and ready to be served from the /opt/front-end/build directory, in the next step we’ll configure our NGINX web server to enable access to it.


# Configure NGINX to serve the Node.JS API and React front-end:

Since our MERN Stack application is made up of two separate projects that both need to be accessed via the same port (HTTP on port 80), we’re going to use NGINX as our public facing web server to receive requests for both the front-end and back-end, and decide where to send each request based on its path. Requests beginning with the path /api/* will be proxied through to the Node.JS api running on port 4000, while other requests will serve the React front-end app.

Follow these steps to configure NGINX for the MERN stack app.

Delete the default NGINX site config file with the following command:
- sudo rm /etc/nginx/sites-available/default

Launch the nano text editor to create a new default site config file with the following command:
- sudo nano /etc/nginx/sites-available/default

Write required code in this file. After updating the file with code, it should look like as shown in the below image.

```
server {
  listen 80 default_server;
  server_name _;
  
  # react app & front-end files
  location / {
    root /opt/frontend/build;
	try_files $uri /index.html;
  }
  
  # node api reverse proxy
  location /api/ {
    proxy_pass http://localhost:4000/api/;
  }
}
```

If you want to enable https connection:
Create a file in NGINX referencing these certificates,

- sudo nano /etc/nginx/snippets/self-signed.conf

The certificate reference file will be,

```
ssl_certificate /opt/backend/security/cert.pem;
ssl_certificate_key /opt/backend/security/cert.key;
```

For forward secrecy, we have to create a strong Diffie-Hellman group,

- sudo openssl dhparam -out /opt/backend/security/dhparam.pem 2048

Now add the following snippet for HTTPS connection in the previous configuration file in /etc/nginx/sites-enabled/client-config,

```
server {
    listen 443 ssl http2 default_server;
    listen [::]:443 ssl http2 default_server;
    include snippets/self-signed.conf;
}
```

The final client-config file will be:

```
server {
  #listen 80 default_server;
  listen 443 ssl http2 default_server;
  listen [::]:443 ssl http2 default_server;
  include snippets/self-signed.conf;
  server_name _;
  
  # react app & front-end files
  location / {
    root /opt/frontend/build;
	try_files $uri /index.html;
  }
  
  # node api reverse proxy
  location /api/ {
    #proxy_pass http://localhost:4000/api/;
    proxy_pass https://localhost:8080/api/;
  }
}
```

Save the file and restart nginx using the following command:
- sudo systemctl restart nginx


# Test your MERN stack application running on AWS (or your home server)
Enter the public DNS name of your AWS EC2 instance (or the ip address of your server) in a browser to access and test your new MERN stack application.
