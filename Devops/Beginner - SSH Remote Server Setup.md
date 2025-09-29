# SSH Remote Server Setup
https://roadmap.sh/projects/ssh-remote-server-setup
(I have partly obscured IPs and other sensitive information)
___
**Info:**
	Setup a basic remote linux server and configure it to allow SSH.

**Start/Complete Date:** 
	29-09-2025 - same day
___
___
## Task
The goal of this project is to learn and practice the basics of Linux. You are required to setup a remote Linux server and configure it to allow SSH connections.

## Requirements
You are required to setup a remote Linux server and configure it to allow SSH connections.

- Register and setup a remote Linux server on any provider e.g. a simple droplet on [DigitalOcean](https://m.do.co/c/b29aa8845df8) which gives you $200 in free credits with the link. Alternatively, use AWS or any other provider.
- Create **two new SSH key pairs** and add them to your server.
- You should be able to connect to your server using both SSH keys.

You should be able to use the following command to connect to your server using both SSH keys.

```bash
ssh -i <path-to-private-key> user@server-ip
```

Also, look into setting up the configuration in `~/.ssh/config` to allow you to connect to your server using the following command.

```bash
ssh <alias>
```

The only outcome of this project is that you should be able to SSH into your server using both SSH keys. Future projects will cover other aspects of server setup and configuration.

Stretch goal: install and configure `fail2ban` to prevent brute force attacks.
___
___
### Solution

- Set up AWS account
- Building my first EC2
	- **Select name & tags** - Test Instance
	- **AMI (Application and OS Images)** - Ubuntu Server 24.04 LTS (HVM),EBS General Purpose (SSD) Volume Type
	- **Instance type** (hardware) - t3.micro
	- **Created new key pair** (below - removed from this documentation)
	- **Configure network** for access (VPC) 
	- **Configure firewall rules**
	- **Configure storage**
	- Review config
	- Started instance

#### Attempts:
```bash
ssh -i C:\Users\*****\Documents\AWS ubuntu@3.107.***.***
# Load key "C:\\Users\\*****\\Documents\\AWS": Operation not supported on socket
# ubuntu@3.107.***.***: Permission denied (publickey).

ssh -i C:\Users\Thiccy\Documents\AWS ubuntu@ip-172-31-***-***.ap-southeast-2.compute.internal
#  ssh: Could not resolve hostname ip-172-31-***-***.ap-southeast-2.compute.internal: No such host is known.

ssh -i "test-env.pem" ubuntu@ec2-3-107-***-***.ap-southeast-2.compute.amazonaws.com
# Warning: Permanently added 'ec2-3-107-***-***.ap-southeast-2.compute.amazonaws.com' (ED25519) to the list of known hosts.
# ubuntu@ec2-3-107-***-***.ap-southeast-2.compute.amazonaws.com: Permission denied (publickey).
```

Instructions from AWS:
Instance ID

***************************** (Test Instance)

1. Open an SSH client.
2. Locate your private key file. The key used to launch this instance is test-env.pem
3. Run this command, if necessary, to ensure your key is not publicly viewable.

```bash
chmod 400 "test-env.pem"
```

4.  Connect to your instance using its Public DNS:

```bash
ec2-3-107-***-***.ap-southeast-2.compute.amazonaws.com
```

Example:
```bash
ssh -i "test-env.pem" ubuntu@ec2-3-107-***-***.ap-southeast-2.compute.amazonaws.com
```

Finally got it working - not sure why it won't allow me to ssh from Windows PC.
- Created a VM on my device using Hyper-V and Lubuntu as the OS.
- Setup OpenSSH server and accessed the VM.
- Created (nano) a replica of the 'Pair Key'.
- Modified the execution permissions of the file.
- Ran the SSH prompt mentioned above.

**Sucessfully connected to it and terminated the EC2 instance**
___
### Solution (ext.)
After researching what could be the error I notieced that I had been using the wrong command to SSH access into the EC2 instance.
I have not changed into the correct directory or called up the 'text-env.pem' file.

Thanks to only being able to SSH into the Hyper-V VM to create and copy the key details, I was forced to already be in the correct directory and run ther script.

___
### Side Notes:

**Configure the network to enable internet access.** You’ll launch your instance into a subnet in your own virtual private cloud (VPC) within the Amazon cloud. A subnet is a range of IP addresses within your VPC. Your default subnet is a public subnet, which means it will assign a public IP address and provide internet access to your instance from outside the Amazon network.  
	**Tip:** For test instances, it’s okay to use the default subnet settings that provide internet access. However, for production instances, it’s best practice to only assign a public IP and use a subnet with internet access when absolutely necessary.


**Set up your firewall.** A security group is a set of firewall rules that control the traffic to your instance. To connect through SSH from your local computer to your instance, you need a rule that allows SSH traffic from your local computer.  
	  **Tip:** The IP address of your local computer might change over time if your internet service provider uses dynamic IP assignment. For test instances, it's okay to allow traffic from any IP address (0.0.0.0/0) so that you can always connect even if your IP address changes. However, for production instances, especially those with sensitive data, it's best practice to allow traffic only from known IP addresses.
