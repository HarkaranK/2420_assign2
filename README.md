# 2420_assign2
# 2420_assign2

<h2>Load Balancer IP: 146.190.0.158 </h2>

<h2>Created By: Harkaran Kang A01292258 </h2>

<h2> Load Balancer </h2>


![image](https://user-images.githubusercontent.com/98972110/205386969-44ca93a2-1027-46d2-a6d2-49b830dd6ac1.png)
* This load balancer contains a **firewall**
* **2 droplets**
* and a **vpc**


<h2> Settings Up Users in Droplets </h2>

<h3> SSH into droplet</h3>

First you must ssh into your droplet as a root user
The command for it is 

```
ssh -i ~/.ssh/<ssh key file> root@<ip address>
```

<h3> Creating users </h3>

Now that you've are inside your droplet as a root user
You can run the following commands to 
* Create a user
* Give them sudo privileges
* Allow yourself to login as your new user
* Give your user a password
* Disable logging in as a root user

**Creating User**
```
useradd -ms /bin/bash <username>
```

**Giving sudo privileges**
```
usermod -aG sudo <username>
```

**Logging in as your user**
```
rsync --archive --chown=<username>:<username> ~/.ssh /home/<username>
```

**Giving user a password**
```
passwd <username>
```

**Disable logging in as root user**
```
sudo vi /etc/ssh/sshd_config
```
* Change **PermitRootLogin yes** to **PermitRootLogin no**

Now you can no longer login as your root user, but only as the user you made