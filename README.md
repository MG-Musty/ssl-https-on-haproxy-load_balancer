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
$ sudo apt install -y certbot python3-certbot-nginx
```

* The python3-certbot-nginx package is a plugin that allows Cerbot to work with Nginx. With certbot installed, we can now proceed to obtain the SSL certificate.

### Step 2) Obtaining SSL Certificate

* Let’s Encrypt provides a number of ways to obtain SSL Certificates using various plugins. Most of the plugins only assist in obtaining the certificate which requires manual configuration of the web server. These plugins are called ‘authenticators’ because they merely check whether the server should be issued a certificate.
* As such, you need to ensure that no service is listening on port 80. To check which services are listening on port 80, run the command.

```
$ netstat -na | grep ':80.*LISTEN'
```

* If Nginx is running on the HAProxy server, stop it as shown.

```
$ sudo systemctl stop haproxy
```

* Next, run certbot using the standalone plugin to obtain the certificate

```
$ sudo certbot certonly --standalone -d www.your-domain_name.tech --non-interactive --agree-tos --email example@gmail.com
```

* If all goes well, the SSL certificate and key will be successfully saved on the server. These files are themselves saved in the `/etc/letsencrypt/archives` directory, but certbot creates a symbolic link to the `/etc/letsencrypt/live/your_domain_name` path.

![SSL haproxy congrat](https://user-images.githubusercontent.com/106968663/214678291-677302c0-f081-4665-8b66-f21cb1ce7367.png)

* Once the certificate has been obtained, you will have the following files in the `/etc/letsencrypt/live/your_domain_name` directory.

* **cert.pem**  – This is your domain’s certificate.
* **chain.pem** – This is Let’s Encrypt chain certificate.
* **fullchain.pem** – Contains a combination of cert.pem and chain.pem
* **privkey.pem** – The private key to your certificate.

### Step 3) Configure HAProxy to use SSL Certificate

* For HAProxy to carry out SSL Termination – so that it encrypts web traffic between itself and the clients or end users – you must combine the `fullchain.pem` and `privkey.pem` file into one file.

* But before you do so, create a directory where all the files will be placed.

```
$ sudo mkdir -p /etc/haproxy/certs
```

* Next, create the combined file using the cat command as follows.

```
$ DOMAIN='www.your-domain_name.tech' sudo -E bash -c 'cat /etc/letsencrypt/live/$DOMAIN/fullchain.pem /etc/letsencrypt/live/$DOMAIN/privkey.pem > /etc/haproxy/certs/$DOMAIN.pem'
```

* Next, secure the file by assign the following permissions to the directory using `chomd command`.

```
$ sudo chmod -R go-rwx /etc/haproxy/certs
```

* Now access the HAProxy configuration file.

```
$ sudo vim /etc/haproxy/haproxy.cfg
```

* In the frontend section add an entry that binds your server’s IP to port 443 followed by the path to the combined key.

```
bind haproxy-ip:443 ssl crt /etc/haproxy/certs/domain.pem
```

* To enforce redirection from HTTP to HTTPS, add the following entry.

```
redirect scheme https if !{ ssl_fc }
```

![correction haproxy ssl](https://user-images.githubusercontent.com/106968663/214680770-7e302b83-0082-4786-8fcd-1c287c609c67.png)

* Save the changes and exit the configuration file. Be sure to confirm that the syntax for HAProxy is okay using the following syntax.

```
$ sudo haproxy -f /etc/haproxy/haproxy.cfg -c
```

* To apply the changes made, restart HAProxy.

```
$ sudo systemctl restart haproxy
```

* And ensure that it is running.

```
$ sudo systemctl status haproxy
```

* Finally go to your browser and refresh your domain.

![SSL set togo](https://user-images.githubusercontent.com/106968663/214681746-d9d9375b-722d-4777-a7f4-fd9e1ce918c6.png)


# 📚 Author :pen:

[Mustapha Aliyu Galadima](https://github.com/MG-Musty/)
