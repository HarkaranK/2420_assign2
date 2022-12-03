# 2420_assign2

<h2>Load Balancer IP: 146.190.0.158 </h2>

<h2>Created By: Harkaran Kang A01292258 Set B</h2>

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

<h2>Caddy File</h2>

In your **wsl** create a **Caddy File**
![image](https://user-images.githubusercontent.com/98972110/205418125-b9dfff70-8d38-4660-8d96-9bc914fa10ac.png)

The content of the Caddy file should be 
```
http:// {
    root * /var/www/html
    reverse_proxy /api localhost:5050
    file_server
}
```

Once again share the ```2420-assign-two``` directory with your droplets

```
rsync -r <directory name> "username@dropletIP:~/" -e "ssh -i /home/<username>/.ssh/<sshkeyname> -o StrictHostKeyChecking=no"
```

![image](https://user-images.githubusercontent.com/98972110/205418380-952c8180-0d26-4f54-a440-bc81879f8d42.png)


In both of the droplets move the **Caddyfile** to the ```/etc/caddy``` directory

```
cd 2420-assign-two
sudo mkdir /etc/caddy/
<password if prompted>
cd ../
sudo cp 2420-assign-two/Caddyfile /etc/caddy/
ls /etc/caddy/
```

![image](https://user-images.githubusercontent.com/98972110/205418517-5844ff2e-b902-499c-898c-cc0eeccb7724.png)

In both of the droplets move the ```caddy``` binary to ```/usr/bin``` directory

```
sudo cp caddy /usr/bin/
```

You can test if everything worked by using the following commands

```
ls /etc/caddy/
ls /var/www/html
ls /var/www/src
ls /usr/bin/ | grep "caddy"
```

![image](https://user-images.githubusercontent.com/98972110/205419774-2abbf4d9-d2a8-4c6b-a5d4-289012b80e75.png)

| If you don't have node_modules or the json files in your ```/var/www/src``` directory run the following |

```
cd /var/www/src/
curl https://get.volta.sh | bash
source ~/.bashrc
volta install node
npm install fastify
```


<h2>Creating Caddy Service File</h2>

Go back to your ```wsl``` and create a caddy.service file in ```2420-assign-two``` directory and enter the following into it 

![image](https://user-images.githubusercontent.com/98972110/205421457-278401a7-8482-4c72-8ad3-154d0016a707.png)

```
[Unit]
Description=Serve HTML in /var/www using caddy
After=network.target

[Service]
Type=notify
ExecStart=/usr/bin/caddy run --config /etc/caddy/Caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target
```

Copy your ```2420-assign-two``` directory to your droplets again

```
rsync -r <directory name> "username@dropletIP:~/" -e "ssh -i /home/<username>/.ssh/<sshkey-name> -o StrictHostKeyChecking=no"
```

![image](https://user-images.githubusercontent.com/98972110/205421735-731c2b97-143f-4216-ab45-72373ba69a5a.png)

Move your caddy.service file into ```/etc/systemd/system/

```
sudo cp caddy.service /etc/systemd/system/
```

In your droplets **reload**,**start**, **enable** and check the **status** of your caddy file 

```
sudo systemctl daemon-reload
sudo systemctl start caddy
sudo systemctl enable --now caddy
sudo systemctl status caddy
```

If everything goes right the output should look like this
![image](https://user-images.githubusercontent.com/98972110/205423182-5d70a90a-b690-4e2b-99a8-7d1c32ff37d8.png)

<h2>Node Service File</h2>

In ```wsl``` create a new file called ```hello_web.service``` in the ```2420-assign-two```

The content of the file should look like this
```
[Unit]
Description=Service file to help run node service
After=network.target

[Service]
Type=simple
User=<username>
Group=<username>
ExecStart=/home/<username>/.volta/bin/node /var/www/src/index.js
Restart=on-failure
RestartSec=5
SyslogIdentifier=hello_web

[Install]
WantedBy=multi-user.target
```

| Be sure to change username to your respective name |

![image](https://user-images.githubusercontent.com/98972110/205423935-8dc5fa8c-b336-47a9-8c31-b0a97a75fd40.png)

![image](https://user-images.githubusercontent.com/98972110/205425283-fd32ba14-969b-4da4-aa53-85561b1fb10a.png)


Send a copy of your ```2420-assign-two``` directory to your droplets

```
rsync -r <directory name> "username@dropletIP:~/" -e "ssh -i /home/<username>/.ssh/<sshkey-name> -o StrictHostKeyChecking=no"
```

![image](https://user-images.githubusercontent.com/98972110/205424219-720d9ae2-a78e-4a28-8638-4746e065b3ad.png)

In your droplets move the ```hello_web.service``` to the ```/etc/systemd/system``` directory

```
sudo cp 2420-assign-two/hello_web.service /etc/systemd/system/
```
![image](https://user-images.githubusercontent.com/98972110/205424287-20e5886a-fb3c-4d62-bd7d-83c1c537b0d5.png)


In your droplets **reload**,**start**, **enable** and check the **status** of your caddy file 

**Reload**, **start**, **enable** and view the **status** of the node service

```
sudo systemctl daemon-reload
sudo systemctl start hello_web
sudo systemctl enable --now hello_web
sudo systemctl status hello_web.service
```

If everything is done correctly it should look like this

![image](https://user-images.githubusercontent.com/98972110/205425369-897d09f2-567f-43cf-84ca-7cb96a1506b4.png)

Now that it is running, you can open up a browser and put your ```load balancers IP```

This is the display of both of my droplets files
![image](https://user-images.githubusercontent.com/98972110/205426020-e930c268-8fee-4bdd-8a4b-190821c8cc38.png)
