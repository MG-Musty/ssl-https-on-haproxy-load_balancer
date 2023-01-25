# HOW TO SETUP SSL-HTTPS ON HAPROXY LOAD_BALANCER

> This tutorial/guide will help you understand how you can setup a ssl-https termination on HAproxy load balancer for security protection on web server.
> HA Proxy is a widely used and opensource HTTP load balancer and proxying solution
>  It’s used to enhance the performance and reliability of web servers by distributing the workload across multiple servers. By so doing, it provides high availability of services and applications.

# SSL Termination

> The incoming traffic that goes through the load balancer is in plain text and is, therefore, insecure and prone to eavesdropping by nefarious third parties.
> HAProxy can be configured to encrypt the traffic it receives before distributing it across the multiple backend servers. This is a preferred approach as opposed to encrypting individual backend servers which can be a tedious process This is where SSL termination comes in.
> The HAProxy encrypts the traffic between itself and the client and then relays the messages in clear text to the backend servers in your internal network.  It then encrypts the response from the backend servers and relays them to the clients.
> The TLS/SSL certificates are stored only on the HAProxy load balancer rather than the multiple backend servers, thus reducing the workload on the servers.

### Step 1) Install Certbot

* To obtain an SSL/TL certificate from Let’s Encrypt Authority, you first need to install certbot. Certbot is free and opensource software that is used for automating the deployment of Let’s Encrypt SSL certificates on websites.

* To install certbot login into the HAProxy server and, first, update the local package index:

```
$ sudo apt update
```

* Next, install certbot using the following command:

```
sudo apt install -y certbot python3-certbot-nginx
```

* The python3-certbot-nginx package is a plugin that allows Cerbot to work with Nginx. With certbot installed, we can now proceed to obtain the SSL certificate.

### Step 2) Obtaining SSL Certificate

