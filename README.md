# PROJECT-8In this project I will enhance my Tooling Website solution by adding a Load Balancer to distribute traffic between Web Servers and allow users to access our website using a single URL. IMAGE 01
![01](https://user-images.githubusercontent.com/91284177/143266677-be602396-fccf-42cd-877b-71b2bb3a5fab.png)

I deployed and configured an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 instance. I made sure that users can be served by Web servers through the Load Balancer.

To simplify, let us implement this solution with 2 Web Servers, the approach will be the same for 3 and more Web Servers.

I opened TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.

I installed Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers: I used the following commands;

#Install apache2
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev

IMAGE 02 and 03

![02](https://user-images.githubusercontent.com/91284177/143272812-0f8c07a2-9dc4-40f6-b5e1-9d5d86ca1eef.png)
![03](https://user-images.githubusercontent.com/91284177/143272825-07e0e71e-7374-4407-b404-db150df25b2d.png)


some modules were enabled using:
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic 


sudo  systemctl restart apache2 (it was run as instructed by the ubuntu terminal)

Apache2 was confirmed to be running, after enabling 

Apache2 was confirmed to be active and running. IMAGE4
![04](https://user-images.githubusercontent.com/91284177/143917677-cacdae03-c1d9-4a85-a18a-f3c806a52725.png)

#Configure load balancing#
The apache root config file was opened and edited. the configuration is telling the apache server to map the Web servers IP addresses to the load balancer such that clients/users can reach the servers through the load balancer.

 IMAGE 5 shows the  inserted private IP addresses of the web servers in the apache2 root configuration file. IMAGE 5 

![05](https://user-images.githubusercontent.com/91284177/147406380-635f042a-3ebc-4c0a-807d-1106fd2d3032.png)

By-traffic balancing method was used for this project. it will distribute incoming load between Web Servers according to current traffic load. this method can control in which proportion the traffic must be distributed by load factor parameter as seen in the apache root. IMAGE 6
![06](https://user-images.githubusercontent.com/91284177/147409316-f3c5a656-d54a-4bf2-9db9-c56c7a4f11c6.png)

I confirmed that the load balancer was properly configured by sending traffic to it via it my browser. its IP address (18.170.35.251) is circled in red on IMAGE 07 below

![07](https://user-images.githubusercontent.com/91284177/147410716-9a0bfb00-7244-440f-b66d-4d6834835926.png)

I used the command <sudo tail -f /var/log/httpd/access_log> to confirm if traffic was sent equally on the two web servers with public IP <18.130.179.98> and <13.40.48.189>. The traffics sent by clients were distributed equally as configured. (loadfactor LB method) IMAGES 08 & 09

![08](https://user-images.githubusercontent.com/91284177/147410919-4338e023-a706-469d-b38f-049deee628c3.png)

![09](https://user-images.githubusercontent.com/91284177/147410921-72267eea-9027-490a-863e-3ce4bc69c010.png)

#Configure Local DNS Names Resolution#
It is tedious to remember and switch between IP addresses, especially if you have a lot of servers under your management. The easiest way to solve this problem is to use /etc/hosts file, although this approach is not very scalable, but it is very easy to configure and shows the concept well. So i will  configure IP address to domain name mapping for my LB.

I opened <sudo vi /etc/hosts> file on my LB and added 2 records into this file with Local IP address and arbitrary name(Web1 and Web2) for both of your Web Servers. IMAGE 10
![10](https://user-images.githubusercontent.com/91284177/147412069-95814f2e-fd74-4e4d-9cd6-e9fd7ea62d1e.png)

This was followed by updating my LB config file with those names instead of IP addresses. IMAGE 11
![11](https://user-images.githubusercontent.com/91284177/147412124-b4beb2d4-4542-49db-95fe-c53453c4997e.png)

l curl my Web Servers from LB locally curl http://Web1 or curl http://Web2 – it shall work. IMAGES 12 & 13
![12](https://user-images.githubusercontent.com/91284177/147412162-cb87e536-ef97-428b-87cb-c186eb8ed02c.png)
![13](https://user-images.githubusercontent.com/91284177/147412170-f41e034a-453f-4050-ab1e-32031d5645b7.png)

At the end of the project i was able to achieve the architecture indicated below. IMAGE 14
![14](https://user-images.githubusercontent.com/91284177/147412199-0447c1b1-c35b-41df-a9fe-1cc37dad2642.png)









