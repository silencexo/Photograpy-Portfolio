# Photograpy-Portfolio
Student Number: 34383348 ICT171 Project

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
- Before proceeding with nginx set-up a domain should be purchased.
To copy files to VM use scp 

    -i /directory of key -r /directory of folder to copy username@serverIP:/destination
