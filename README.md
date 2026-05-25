# Photograpy-Portfolio
Student Number: 34383348 ICT171 Project


https://jtbeaman.com
## How to guide for VM Webserver Outline
1. Choose a IAAS cloud VM provider such as Microsoft azure, Amazon AWS, Google cloud etc
3. Configure VM
4. Purchase a domain name
5. Configure DNS record
6. Configure Nginx
7. Install and run certbot to enable HTTPS

### Choosing a cloud provider
- For my webserver I have chosen to use Microsoft azure and have chosen the Standard B2ats v2 VM. The VM that I have chosen has 1GB of memory and 64GB of storage. 
- Depending on the website requirements, more powerfull VM configurations can be used.   


### Configure VM
- On the cloud providers website, create a Key pair so that only computers with the key can connect to the VM
- Configure inboud port rules, enable inbound port:80 (HTTP) and port:443 (HTTPS)
- If using Microsoft azure an existing SSH inboud rule will be enable to allow connections to be made, if using another provider make sure there is an SSH rule to connect
- Connect to your VM
- Open a terminal on mac or powershell in windows and cd to the location where your key is saved
  
      ssh -i "yourkeyname.pem" username@VM-PublicIP
- Once connected to the VM update apt repos with

      sudo apt update
- Install nginx

      sudo apt install ngninx-full
### Purchase a domain name
- Before proceeding with nginx set-up a domain should be purchased
- For this project I purchased a domain with spaceship.com, however there are many domain name providers
- The next step will be different depending on where the domain was purchased from but essentially we need to create a propergating DNS record to point computers that attempt to connect to our domain, to connect to the public IP address of our VM.
- On spaceship I navigated to their "Advanced DNS" page and created a new record
- The record type should be "A", and the IPv4 should be the one that VM uses.
- After propergation the VM should now be accessible from the Domain in addition to the IPv4

### Nginx Set-up
  
- Start nginx with

      sudosystemctl start nginx
- Before doint anthing else, with ngninx running test if the webserver is running by entering your VM's IPv4 in a web browser (the welcome to nginx page should be visable)
- Also test whether the DNS record works by using your domain name instead of the IP address
- cd into the ngninx sites-available folder

      cd /etc/nginx/sites-available
- Copy make a copy of the default site configuration to the new name of the project

      cp default project
- Use nano to edit our project configuration

      nano project
- Chage the "server_name" field to the name of the domain (server_name jtbeaman.com)

- Change the root from /var/www to /var/www/project (This is wherer we will place our website files)
- Create the folder that we just specified in the configuration file

      cd /var/www
      mkdir project
  
- Next some changes need to made to the default configuration too

      cd /etc/nginx/sites-available
      nano default
- Remove any "default_server" lines from this file as nginx does not allow duplicate default webservers

- Now we need to create a symbolic link to our new site in sites-enabled with the following command

      ln -s /etc/nginx/sites-available/project /etc/nginx/sites-enabled/
- Check that the symbolic link was corretly created

      cd ..
      ll sites-enabled
- Next we need to actually create and place our files into
- As our VM is only accesible via the terminal and does not have a GUI I opted to create and edit the HTML for my website on my local machince and then once complete copy the files into the VM
- To copy the HTML files to VM use scp (Note, due to permissions you will likely need to transfer the files to the home folder first and then into the /var/www/project directory)

      scp -i /directory of key/key.pem -r /directory of folder to copy username@serverIP:/destination
- For example the command I used is below, your host file structure will likely be different

      scp -i /DirectoryOfKey/key.pem -r /Users/josh/Desktop/project josh@20.28.241.243:/home/josh
- Once the project folder is on the VM in the home folder use a cp command to copy the contents of the folder to our project folder in our /www directory

      sudo cp /home/username/project/* /var/www/project

- Finally restart Nginx and go test out the website

      sudo systemctl reload nginx

### Cerbot HTTPS certificate set-up


- Firstly make sure that snapd is installed with


        install snapd
- Next install certbot with


        sudo snap install --classic certbot


- Run certbot and have it automatically edit our nginx configuration run HTTPS


        sudo certbot --nginx

- Finnally double check the website works now connecting with a HTTPS url

## Bash Script Set-Up


