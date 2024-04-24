# Manual and Automatic Deploy for Flask App powered ML Model on AWS EC2 Instance amazon linux


## Image
<div style="text-align:center;">
    <img src="https://github.com/Baraa-Rayed/ML_Flask_app/assets/101131013/3150465d-69d0-4c02-8567-de065fed2104" alt="hqdefault">
</div>

## 🤖 Introduction


In this tutorial, we'll guide you through the process of deploying a ML Flask web application on an AWS EC2 instance for predecting the salary. Flask is a lightweight web application framework written in Python, and AWS EC2 (Elastic Compute Cloud) provides scalable computing capacity in the cloud.
## 📋 <a name="features">Features</a>

## 🔋 Features

- Deploy a Flask web application on AWS EC2.
- Use Gunicorn as the WSGI server.
- Configure Nginx as a reverse proxy.


## 📋 <a name="table">Table of Contents</a>

## ⚙️ Tech Stack

- Python
- Flask
- AWS EC2
- Gunicorn
- Nginx

# Part 1:
## Manual way:

## 🧰 Getting Started

### Prerequisites

- An AWS account
- Basic knowledge of Python and Flask
- Familiarity with the Linux command line

## **Steps**

### **1. Clone this project to EC2**

First, SSH into your EC2 instance and update the package list or connect to it by using aws console:

```bash
sudo su
yum update -y
```
Second, install git by :

```bash
yum install -y git
```

### **2. Install Python Virtual Environment**

Then, install the Python virtual environment package:

```bash
yum install -y python3-pip
```
### **2. Clone the repository**
```bash
git clone https://<Your User Namer>:<github Token>@github.com/<Your User Namer>/ML_Flask_app.git
```


### **3. Set up the Virtual Environment**

Create a new directory for your Flask app and navigate into it:

```bash
cd ML_Flask_app
```

Create a virtual environment named 'env':

```bash
python3 -m venv env
```

Activate the virtual environment:

```bash
source env/bin/activate
```

### **4. Install project requirements**

Within the virtual environment, install **requirements.txt**:

```bash
python3 -m pip install -r requirements.txt
```

### **5. Create gunicorn service file for managinig the ML Flask app**

Create a file named **`ML_Flask_app.service`** 

```bash
sudo nano /etc/systemd/system/ML_Flask_app.service
```
paste this configuration into it:

```
[Unit]
Description=Gunicorn instance for a simple ML flask app
After=network.target

[Service]
User=ec2-user
Group=ec2-user
WorkingDirectory=/home/ec2-user/ML_Flask_app
ExecStart=/home/ec2-user/ML_Flask_app/env/bin/gunicorn -b localhost:8000 app:app
Restart=always

[Install]
WantedBy=multi-user.target
```
Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl start ML_Flask_app
sudo systemctl enable ML_Flask_app
```

### **6. Verify Flask App is runining**

```bash
sudo systemctl status ML_Flask_app
```

or

Check if the app is running with

```bash
curl localhost:8000
```

**Gunicorn is running (Ctrl + C to exit gunicorn)!**


Run Nginx Webserver to accept and route request to Gunicorn Finally, we set up Nginx as a reverse-proxy to accept the requests from the user and route it to gunicorn.

### **8. Set up Nginx as a Reverse Proxy**

Install Nginx:

```bash
yum install -y nginx
```

Start and enable the Nginx service:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

Edit the Nginx default configuration file:

```bash
sudo nano /etc/nginx/nginx.conf
```

Add the following code at the top of the file (below the default comments)

```
upstream flaskML_Flask_app {
    server 127.0.0.1:8000;
}

```

Add a proxy_pass to flaskML_Flask_app atlocation /

```
location / {
    proxy_pass http://flaskML_Flask_app;
}
```
After add the code to the nginx confg. file, should look like below:

```
# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    upstream flaskML_Flask_app {
        server 127.0.0.1:8000;
    }

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        location / {
            proxy_pass http://flaskML_Flask_app;
        }

        error_page 404 /404.html;
        location = /404.html {
            root   /usr/share/nginx/html;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
}
```

Restart Nginx for changes to take effect:

```bash
sudo systemctl restart nginx
```

Now, your Flask application should be accessible via your EC2 instance's public IP address.


# Part 2:
## Automatic way:


just upload the **mldemocf.yaml** stack to the cloud formartion and then the only thing that you need to do manually is to provide the nginx configiration file  with:

```
upstream flaskML_Flask_app {
    server 127.0.0.1:8000;
}

```

Add a proxy_pass to flaskML_Flask_app atlocation /

```
location / {
    proxy_pass http://flaskML_Flask_app;
}
```
Congratulations! You have successfully deployed a Flask web application on an AWS EC2 instance using Gunicorn and Nginx as a reverse proxy.


## 📋 <a name="table">Table of Contents</a>
## ⭐ Give A Star

You can also give this repository a star to show more people and they can use this repository.


## 📚 Learn More
Of course! Here are the resources with descriptions and direct links:

**Python:**
1. **Official Python Documentation**: The Python Software Foundation maintains comprehensive documentation covering all aspects of Python, from beginner to advanced topics.
   - [Documentation Link](https://www.python.org/doc/)

**AWS EC2:**
1. **AWS Documentation - EC2**: The official AWS documentation provides comprehensive guides and tutorials for using EC2 instances, covering everything from launching instances to managing security and scalability.
   - [Documentation Link](https://docs.aws.amazon.com/ec2/index.html)

2. **AWS Certified Solutions Architect - Associate Certification Guide**: This guide covers all the topics you need to know to become certified in AWS, including EC2 instances.
   - [Book Link](https://www.amazon.com/Certified-Solutions-Architect-Associate-All/dp/1119138558)

3. **AWS in Action by Andreas Wittig and Michael Wittig**: This book provides practical examples and insights into using various AWS services, including EC2.
   - [Book Link](https://www.amazon.com/AWS-Action-Andreas-Wittig/dp/1617295116)

**Gunicorn:**
1. **Gunicorn Documentation**: The official Gunicorn documentation provides information on installing, configuring, and using Gunicorn as a WSGI server for Python web applications.
   - [Documentation Link](https://docs.gunicorn.org/en/stable/)

**Nginx:**
1. **Nginx Documentation**: The official Nginx documentation provides detailed information on installing, configuring, and using Nginx as a web server and reverse proxy.
   - [Documentation Link](https://nginx.org/en/docs/)

2. **Nginx Essentials by Valery Kholodkov**: This book covers the essentials of Nginx configuration and usage, making it a good resource for beginners.
   - [Book Link](https://www.amazon.com/Nginx-Essentials-Valery-Kholodkov/dp/0985193208)

3. **DigitalOcean Tutorials**: DigitalOcean offers numerous tutorials on web development and server administration topics, including configuring Nginx.
   - [Tutorial Link](https://www.digitalocean.com/community/tutorials)

