---
layout: post
title:  "Security Onion Home Lab Part 1"
date:   2025-10-10 19:30:00 -0700
categories: soc
---

# Security Onion 2.4 Overview
**What is Security Onion**

According to [Security Onion Soluitons](https://securityonionsolutions.com/software/):

    "Security Onion is a free and open platform built by defenders for defenders. It includes network visibility, host visibility, intrusion detection honeypots, log management, and case management."

**Tech Stack used in Security Onion**

**Network Visability**
- **Suricata** – Signature based NIDS, packet capture, & metadata and file extraction, & file analysis
- **Zeek** – Network metadata collection & file analysis
- **Stenographer** – Full packet capture 
- **OpenCanary** - Intrustion Detection Honeypot

**Host Visability**
- **Elastic Agent** - Endpoint visability & data collection
- **OsQuery** - Live queries embedded in Elastic Agent
- **Elastic Fleet** - Centralized management

**Security Onion Console (SOC)**
- **Elastic Stack** - **Elasticsearch** used for data storage, **Logstash** used to ingest, parse and process logs, and **Kibana** used for visualization. **SOC** is a customized user interface for alerting, hunting, dashboards and more.

**Additional Tools**
- **Strelka** - File analysis
- **Redis** - Message broker (log buffer)
- **Sigma** - Detection rule format
- **Wireshark** - Packet analysis
- **CyberChef** - Data decoding utility
- **Att&ck Navigator** - Advesary mapping framework

By no means is this the full list of technologies used but with this you can get a pretty good idea of the capabilities of Security Onion. 

# Security Onion Installation
Go to (https://securityonionsolutions.com/software/) and click download. 

It will bring you to this Github repo. Download the ISO file.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-24 124303.png)

Optional but highly recommended. Verify the hashes are the same using "Certutil" 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-24 180858.png)

Before beginning you can go to (https://docs.securityonion.net/en/2.4/hardware.html) and check out the mimimum hardware requirements to run this lab. For this lab I chose the Standalone architecture.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-12 172610.png)

Now open VMware Workstation and click Create a New Virtual Machine. 

The pop up wizard will come up. Keep the default configuration.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 121320.png)

Select "Installer disc image file" and browse to the ISO file you just downloaded. *I created a separate folder for my ISO. You may do that or just use your downloads folder. For example, C:\Users\username\Downloads\ 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 121839.png)

Name your virtual machine and choose the location you want to save it at.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 121858.png)

Put 200 GB for disk size (See Minimum Specs chart) and select "Store virtual disk as a single file".

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 121915.png)

Before finishing the installation, click on "Customize Hardware".

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 123919.png)

Select Memory and choose 24 GB or more. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 123858.png)

Select Processors and change "Number of cores per processor" to 2. You should now have a total of 4 CPU cores.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 123441.png)

Now we need to have two NICs (Network Interface Card). The first one needs to be connected to NAT so that we can connect and communicate with Security Onion from our other machine. This will be for security management purposes. The second NIC will be for logs and monitoring purposes. 

Select Network Adapter and click on Add down at the bottom. Select Network Adapter and Finish. 

Select the second Network Adapter and change to Host-only. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 123625.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 123815.png)

Close the hardware window and select Finish. 

# Security Onion Configuration

From this point on your mouse will be disabled. Use the Up and Down arrow key to highlight your selection and hit Enter to select it. Tab key allows you to move between different sections on the screen. 

*A couple things to note. On the boot menu I want to highlight three of these options. First is the standard install shown at the top. This will be the option we choose to access the console (SOC). The second option I want to highlight is the IDH Node. This option installs a honeypot node that reports to the central manager. It can only be used in distributed deployments for enterprises. The last option I want to highlight is Pro. I'm using this option in my SANS lab coursework and it's awesome! By default Security Onion comes with whats called Emerging Threats (ET), which is a set of intrusion detection rules. In the free open source version it's called ETOPEN and is community driven, meaning contributions come from the security community. With the paid license, called ETPRO, these rulesets offer more advanced threat detection capabilities, guaranteed message delivery, use of compliance tools (STIG), fewer false positives, and much more.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 124202.png)

Now Security Onion will download everything it needs. This may take a bit. Once done you will see the Warning banner below, type the entire word 'yes' and hit enter. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 125441.png)

Create an admin username, hit enter. 
Create a password, hit enter. *Remember in Linux systems the terminal doesn't show your password. 
Re-enter the password again, hit enter. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 125721.png)

This downloading process is going to take a while. 
Once complete you will need to reboot. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 175228.png)

Once reboot is complete you may sign in using your credentials. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 180102.png)

Follow the screenshots below. The blue highlight is the option to choose. Use tab to move from the highlighted option to 'YES', 'NO', 'Ok' or 'Cancel'. Once blue is on the correct option, hit tab, and once either 'YES' or 'OK' is highlighted red hit Enter. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 192533.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 192702.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 192743.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 192846.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 192923.png)

Create a hostname.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 193012.png)

The blue highlighted 'ens160' is the network adapter using NAT. The other option 'ens192' is the Host-Only NIC. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 193146.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 193302.png)

Here we are going to assign the IP range manually. If you don't know the subnet address to connect to, in the menu bar of VMware Workstation go to 'Edit', and click 'Virtual Network Editor'.

The editors interface shows two virtual networks. The IP address we want is VMnet8 NAT, select it. 
You will see down at the bottom the 'Subnet IP' and 'Subnet mask'. The IP range we need to manually add in the Security Onion VM needs to be the same first 3 octets as the 'Subnet IP'. 
My 'Subnet IP' was 192.168.8.0/24. The /24 comes from the 'Subnet mask'. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 112925.png)

Next click 'NAT Settings in the middle right. Here you will the 'Subnet IP', 'Subnet mask', and 'Gateway IP' - the routers IP that allows the Security Onion VM to connect to the internet. Write this down, you will need it.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 113124.png)

The IPv4 address with CIDR mask I assigned to Security Onion was 192.168.8.*100*/24. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194226.png)

Next enter the 'Gateway IP'. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194307.png)

For the DNS server keep this default, this is Google's DNS.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194337.png)

And again keep the domain default.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194355.png)

Continue to follow the screenshots.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194428.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194458.png)

Here you will need to hit the space bar to select 'ens192', and a '*' should appear. Remember this is the Host-Only NIC that is used for logs and monitoring. Hit tab to to select 'Ok'. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194552.png)

Now you can enter any email address you like, it doesn't need to be a real one. You can make it up like I did. This will allow you to sign into the SOC interface. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194646.png)

Create a password for the email address just created. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-26 194822.png)

Follow screenshots.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-27 083325.png)

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-27 083345.png)

Enter the same IP address as the 'Subnet IP'. Make sure to add the 'Subnet mask' in CIDR notation /24.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-27 083712.png)

It's up to you on which to select, 'Yes' or "No'. This is a lab environment so it doesn't matter.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-27 083806.png)

This is the summary of the configurations we just made. Tab to 'Yes' and hit Enter. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-27 084011.png)

Now it will start to reboot and apply the configuration. Again this will take a while. 

Once complete, tab to 'Ok" and hit Enter. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-27 102528.png)

You are now interacting with Security Onion. Run the command
> sudo so-status

and enter your password.

Here you will see docker containers running the Tech Stack mentioned above. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-09-27 102657.png)

To test the connection, ping the Security Onion VM from another system. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 152031.png)

Now ping the other system from Security Onion command line.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 153659.png)

On your other machine, using either Firefox or Chrome go to the SOC web interface by typing the IP address into the address bar. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 154319.png)

This warning will pop up. That's ok go ahead and proceed. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 154344.png)

Sign in using the created email and password.

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 154545.png)

Congrats you have successfully installed and configured a Security Onion Home Lab. 

![Screenshot](/assets\images\2025-10-12-Security Onion\Screenshot 2025-10-13 161501.png)

**Part 2 to follow**