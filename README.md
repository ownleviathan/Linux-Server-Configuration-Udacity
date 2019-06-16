# Linux-Server-Configuration-Udacity
Udacity Nanodegree Program - Full Stack Web Developer
# **Project Introduction**

These are the steps to install a Linux Server and prepared it for Web applications.

**Server Details**
- **IP address** : 18.196.167.64
- **SSH login username:** grader
- **SSH port :** 2200
- **URL :** http://3.85.137.110/

# Configuration Steps

**1. Create an instance in AWS lightsail**

- Go to[ AWS Lightsail](https://lightsail.aws.amazon.com/ " AWS Lightsail") and create a new account.
- Click Create instance and choose `Linux/Unix`, `OS only`, `Ubuntu 18.04LTS`
- Select month payment
- Click on `Create` button


**2. Set up SSH key**

On your local machine create a public and private key pair.
Run the next command:
`$ ssh-keygen`
Follow the instructions and the you can get 2 files **id_rsa** and** id_rsa.pub**
> **Note: **I ran the command using Git-bash for windows. The files where stored in C:\Users\account_name\\.ssh


**3. Change SSH port**

Open the following file:
    `nano /etc/ssh/sshd_config`
Modify` #Port 22` for `Port 2200`
Restart SSH service: `service ssh restart`


**4. Configure Timezone to Use UTC**

Run the command:
`# sudo dpkg-reconfigure tzdata`
Then select `None of the Above` and then choose `UTC`

**5. Setup Uncomplicated Firewall  (UFW)**

