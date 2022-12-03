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

<h2>Installing Caddy</h2>

To install Caddy run the following commands

```
wget https://github.com/caddyserver/caddy/releases/download/v2.6.2/caddy_2.6.2_linux_amd64.tar.gz
```

![image](https://user-images.githubusercontent.com/98972110/205418984-8cf73b72-eae6-4ab9-be71-48fd5cf0374e.png)


Extracting the ```tar.gz``` file

```
tar -xvf caddy_2.6.2_linux_amd64.tar.gz
```

![image](https://user-images.githubusercontent.com/98972110/205418955-b2f30cf5-e271-4f24-9498-a83fe8bb7b1f.png)


<h2>Writing Web Apps</h2>

<h3>Creating Directories</h3>

* In your **~** directory you will create a directory called 2420-assign-two
* cd into it and create 2 more directories **html** & **src**
![image](https://user-images.githubusercontent.com/98972110/205395241-014f761e-7110-44ec-89da-2be1d0bbfaf2.png)

<h3>Creating Files</h3>

* cd into your html directory
* Create a file called index.html

![image](https://user-images.githubusercontent.com/98972110/205395768-5fb9fadd-b912-4829-b6d3-03c90cd09a3f.png)

* Fill the html file with some appropriate code
(This is mine for refernce)
![image](https://user-images.githubusercontent.com/98972110/205396573-f9b26d20-b537-49c5-b0cc-b9757cc4c832.png)


* cd into your src directory
* Create a file called index.js

![image](https://user-images.githubusercontent.com/98972110/205396301-7258a996-fafc-4f0c-92e3-c8dc2eb10c99.png)

* Fill the index.js file with the following code 
```
// Require the framework and instantiate it
const fastify = require('fastify')({ logger: true })

// Declare a route
fastify.get('/', async (request, reply) => {
  return { hello: 'Server x' }
})

// Run the server!
const start = async () => {
  try {
    await fastify.listen({ port: 3000 })
  } catch (err) {
    fastify.log.error(err)
    process.exit(1)
  }
}
start()
```
<h3>Installing Volta & Fastify</h3>
Installing Volta and Fastify is the last part of this step
Installing it is necessary to host the file on your machine


You may not have npm installed, if unsure run the following
```sudo apt install npm```

Installing **Volta**

```curl https://get.volta.sh | bash```

```volta install node```

```volta install npm```

Installing **Fastify**

```npm init```

``` npm install fastify```


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
| Make Sure to Install these on the droplets too |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


<h3>Hosting Index.js</h3>

Now that the files have been created and Volta is installed we can finally host the file
Run the command 
```
node index.js
```
On your browser search **localhost:3000**
![image](https://user-images.githubusercontent.com/98972110/205398529-359ffc7a-84be-4e3a-a5db-c24f3b87f296.png)

<h3>Uploading to Droplets</h3>

Run the command
```
rsync -r <directory name> "username@dropletIP:~/" -e "ssh -i /home/<username>/.ssh/<sshkey-name> -o StrictHostKeyChecking=no"
```
![image](https://user-images.githubusercontent.com/98972110/205402003-2cdd9037-9fcb-4abb-80f2-e8212de8f18d.png)

<h3>Moving web apps</h3>

In your droplets move the ```index.html``` file to the ```/var/www/html``` directory

```
sudo mkdir -p /var/www/html/
sudo mv ~/2420-assign-two/html/index.html /var/www/html/
```

![image](https://user-images.githubusercontent.com/98972110/205419428-9ec963eb-8946-4075-ac59-252c810514a6.png)

Moving the ```2420-assign-two/src``` directory to the ```/var/www/``` directory

```
sudo mv ~/2420-assign-two/src /var/www/
```

![image](https://user-images.githubusercontent.com/98972110/205419498-9a1d3e51-fb80-4a83-a77f-8454c19f2f6e.png)