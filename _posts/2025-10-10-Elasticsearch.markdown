---
layout: post
title:  "Elasticsearch Instance Spin Up"
date:   2025-10-10 19:30:00 -0700
categories: siem
---

# Deploy Elasticsearch on Vultr
**Create a Vultr Instance**

Sign in or sign up for an account in Vultr. Once logged in make sure you are under products<sup>1</sup>, click Network<sup>2</sup>, select VPC 2.0<sup>3</sup>, and click "ADD VPC 2.0 Network".<sup>4</sup>
![Screenshot](\assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 132956.png)

Choose the Network Location of your choice. Make sure all the virtual machines you make match this location.

Under Configure IP Range select "Configure IPv4 Range"<sup>1</sup> and manually set an IP range<sup>2</sup>. Give the network a name<sup>3</sup>, and click Add Network.<sup>4</sup>
![Screenshot](\assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 133633.png)

Now the VPC cloud network is complete. 

**Create Elasticsearch VM**

In the top left corner click "Deploy" and select Deploy New Server.<sup>1</sup>
![Screenshot](\assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 134103.png)

Leave default choice as Dedicated CPU<sup>1</sup> and choose the same location as the VPC network<sup>2</sup>
*Something I thought was cool, notice the Available Services and Compliance that different locations have. 
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 134307.png)

For this lab I chose 4 vCPUs, 16 GB RAM, and 80 GB of storage.
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 134422.png)

Move to the next page and under Operating System select Ubuntu and for this lab I selected version 22.04 x64<sup>1</sup>
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 134613.png)

Click to disable Auto Backups.
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 134715.png)

IPv4 will be automatically checked. Select VPC Network.<sup>1</sup>
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 134836.png)

Under Server Settings give the Server 1 Host a name<sup>1</sup> and leave everything else default.
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 134952.png)

Check over the Summary, make sure everything is correct and hit Deploy.<sup>1</sup>
*Note the hourly price. This whole lab can be done for less than 50 cents if ran for a few hours.
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 135019.png)

Once Status says "Running"<sup>1</sup>, click on your server name "ELK".<sup>2</sup>
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 135329.png)

While the server installs and boots up, you can view the console.<sup>1</sup>
*Keep note of the username and IP address.<sup>2</sup> (Your IP address will be different than mine)
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 135358.png)

Server is ready to go once prompted to login.
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 135518.png)

Instead of working through the console another way to interact with the VM is to SSH into it. 
*I used my student Windows VM that I got from SANS. It wouldn't allow screenshots. These are the powershell commands to run next.
> ssh root@155.138.243.101    

#Use IP from dashboard

#Enter Password from dashboard

> apt-get update && apt-get upgrade -y

#Updating the repositories

#Hit enter when you see a window pop up

**Install Elasticsearch**

Time to install Elasticsearch. Go to (https://www.elastic.co/downloads/elasticsearch) For platform choose "deb x86_64<sup>1</sup>
Once selected hover over the blue button and right-click and select "Copy link address".
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 144227.png)

Go back to powershell and follow these commands.
> wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.5-amd64.deb

#Once downloaded run

> ls

#You should see the debian file listed - elasticsearch-9.1.5-amd64.deb 

#Keep in mind you may have a different elasticsearch version number

> dpkg -i elasticsearch-9.1.5-amd64.deb 

#To install Elasticsearch

###Once finished, look under the "Security autoconfiguration information". This shows your password for your superuser and additonal information on password reset. Go ahead and highlight everything under the title and save it in whatever text editor you'd like just in case you may need it. For me I saved it in notepad.###

> cd /etc/elasticsearch

> ls

#This is where the configuration file is for your elastic search vm. It is a YAML file

> nano elasticsearch.yml

Now there are a couple things that need to be changed. First find "#network.host: 192.168.0.1" by hitting the down arrow button.

This will allow your Elasticsearch instance to be accessible via your SOC analyst laptop (laptop you use to SSH). 

Change the network host by 1. Removing the comment "#" 2. Change the default IP address to your laptops IP address.

Hit the down arrow and remove the comment "#" in front of "#http.port: 9200"

Hit "Ctrl + X" save then "Ctrl + Y" and enter

**Configure Firewall Settings**

In the Elk Server dashboard click Settings,<sup>1</sup> Firewall<sup>2</sup>, and Manage.<sup>3</sup>
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 150342.png)

In the top right corner cick "Add Firewall Group.
Name your Firewall Group and click Add Firewall Group.
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 150105.png)

By default it has an SSH rule that allows anywhere from the internet to access the VM. For security lock it down to just the IP address you used to SSH into the server. Under Source select "My IP"<sup>1</sup> and add your IP address.<sup>2</sup>
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 150151.png)

In the top left corner click Compute, and select the VM "ELK". Now In the Elk Server dashboard click Settings,<sup>1</sup> Firewall<sup>2</sup>, Manage,<sup>3</sup> and select the Firewall Group just created,<sup>4</sup> and hit Update Firewall Group. 
![Screenshot](/assets\images\2025-10-10-Elasticsearch\Screenshot 2025-10-09 150342-2.png)

**Fire up Elasticsearch**

Head back over to powershell and start up the Elasticsearch instance. 

> systemctl daemon-reload

> systemctl enable elasticsearch.service

> systemctl start elasticsearch.service

> systemctl status elasticsearch.service

#Check that status says "Running"

Congrats Elasticsearch is spun up. 
