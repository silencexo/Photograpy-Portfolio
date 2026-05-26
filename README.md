# Photography-Portfolio 
Student Number: 34383348 ICT171 Project


Live website link: https://jtbeaman.com
## IAAS Cloud VM Nginx Documentation
1. Choose an IAAS cloud VM provider such as Microsoft Azure, Amazon AWS, Google cloud etc
3. Configure VM
4. Purchase a domain name
5. Configure DNS record
6. Configure Nginx
7. Install and run certbot to enable HTTPS

### Choosing a cloud provider
- For my web server, I have chosen Microsoft Azure and the Standard B2S v2 VM. The VM that I have chosen has 1GB of memory and 64GB of storage. 
- Depending on the website requirements, more powerful VM configurations can be used; however, the lower tier that has been chosen here will be sufficient for a lightweight, basic website.   


### Configure VM
- On the cloud providers website, create a Key pair so that only computers with the key can connect to the VM
- Configure inboud port rules, enable inbound port:80 (HTTP) and port:443 (HTTPS) (The HTTP port can be disabled when an SSL certificate has been aquired)
- If using Microsoft azure an existing SSH inboud rule will be enable to allow connections to be made, if using another provider make sure there is an SSH rule to connect
- Connect to your VM
- Open a terminal on mac or powershell in windows and cd to the location where your key is saved
  
      ssh -i "yourkeyname.pem" username@VM-PublicIP
- The command above uses -i followed by the name or location of the private key, as connecting to the VM with a password is less secure. For more information on SSH key pairs and how to generate them, visit https://www.ssh.com/academy/ssh/public-key-authentication
- If you have configured the server with an SSH password instead of a key pair, the command to connect should look like

      ssh username@VM-PublicIP 
- Once connected to the VM, update apt repos with

      sudo apt update
- Install nginx

      sudo apt install ngninx-full
### Purchase a domain name
- Before proceeding with nginx set-up a domain should be purchased
- For this project I purchased a domain with spaceship.com, however there are many domain name providers
- The next step will be different depending on where the domain was purchased from but essentially we need to create a propergating DNS record to point computers that attempt to connect to our domain, to connect to the public IP address of our VM
- On Spaceship's website, I navigated to their "Advanced DNS" page and created a new record
- The record type should be "A", and the IPv4 should be the one that VM uses
- After propergation the VM should now be accessible from the Domain in addition to the IPv4

### Nginx Set-up
  
- Start nginx with

      sudosystemctl start nginx
- Before doing anything else, with nginx running, test if the webserver is running by entering your VM's IPv4 in a web browser (the welcome to nginx page should be visible)
- Also test whether the DNS record works by using your domain name instead of the IP address (DNS record propagation can take some time)
- cd into the nginx sites-available folder

      cd /etc/nginx/sites-available
- Copy make a copy of the default site configuration to the new name of the project

      cp default project
- Use nano to edit our project configuration

      nano project
- Change the "server_name" field to the name of the domain, e.g. "server_name jtbeaman.com"
- Change the root from /var/www to /var/www/project (This is where we will place our website files)
- Create the folder that we just specified in the configuration file

      cd /var/www
      mkdir project
  
- Next, some changes need to be made to the default configuration too

      cd /etc/nginx/sites-available
      nano default
- Remove any "default_server" statements from this file, as nginx does not allow duplicate default web servers
- Now we need to create a symbolic link to our new site in sites-enabled with the following command. The command ln makes a new link, -s specifies that we are making a symbolic link, then finally we simply write the directory where the link points to followed by the directory where the link should be created.

      ln -s /etc/nginx/sites-available/project /etc/nginx/sites-enabled/
- Check that the symbolic link was correctly created

      cd ..
      ll sites-enabled
- Next we need to actually create and place our files into the /var/www/project folder
- As our VM is only accesible via the terminal and does not have a GUI I opted to create and edit the HTML for my website on my local machince and then once complete copy the files into the VM
- To copy the HTML files to VM use scp (Note, due to permissions you will likely need to transfer the files to the home folder first and then into the /var/www/project directory)

      scp -i /directory of key/key.pem -r /directory of folder to copy username@serverIP:/destination
- For example, the command I used is below

      scp -i /DirectoryOfKey/key.pem -r /Users/josh/Desktop/project josh@20.28.241.243:/home/josh
- Once the project folder is on the VM in the home folder, use a cp command to copy the contents of the folder to our project folder in our /www directory. Sudo is used as we are copying files to a protected directory. The -r argument specifies that all files should be copied, including folders within folders, and the asterisk at the end of the local directory signifies we are copying the files, not the folder itself, as we have already created the folder var/www/project.

      sudo cp -r /home/username/project/* /var/www/project

- Finally, restart Nginx and attempt to visit the website

      sudo systemctl reload nginx

### Cerbot HTTPS certificate set-up


- Firstly, make sure that snapd is installed with


        install snapd
- Next install certbot with


        sudo snap install --classic certbot
- Run certbot and have it automatically edit our nginx configuration to run HTTPS


        sudo certbot --nginx

- Finally, check that the website works by connecting with a HTTPS URL

## Bash Script Set-Up


For my project, I have decided to make a script that visually outputs to the command line the current disk usage, then checks for and updates any packages, making sure to restart nginx afterwards in case it was updated.

```
echo "DISK CHECKER AND UPDATER SCRIPT"

# Show current date and time
echo "Date: $(date)"
echo ""

# Show uptime
echo "System Uptime:"
uptime -p
echo ""


echo "Disk Usage (Root Partition):"
# This line prints the disk usage stats for the root directory
df -h /
echo ""

echo "Checking for updates"

sudo apt update && sudo apt upgrade

echo "Restarting Nginx"

sudo systemctl restart nginx
```

- To create a bash script like the one above, first cd into the directory where it will be stored

      cd /var/www/project
- Create a new bash file with nano

      sudo nano script.sh
- Write into this file the bash script
- Make the script executable with

      sudo chmod +x script.sh
- Finally execute the script with

      ./script.sh
- The output of my script looks like this
<img width="348" height="181" alt="Screenshot 2026-05-26 at 12 55 58 pm" src="https://github.com/user-attachments/assets/fafef305-3bda-450e-bcec-7ff81d173954" />
<img width="531" height="366" alt="Screenshot 2026-05-26 at 12 56 57 pm" src="https://github.com/user-attachments/assets/a2e2a8f1-d6ae-4841-b4a2-9910d011c7e6" />

