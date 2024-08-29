# Project 3: Setup Load Balancing for Static Website Using Nignx #

This project aims to teach Layer 7 load balancing and load balancing algorithms using Nginx as a Load Balancer

## Type of Load Balancer

This is a reverse proxy load balancer configuration in Nginx, where Nginx acts as an intermediary for requests from clients seeking resources from the servers behind it. It's capable of distributing traffic across the servers listed in the upstream block.

###	Project Tasks ###
1	Deploy three servers
2	Set up static websites on two servers using Nginx.
3	Use two separate HTML files with distinct content. Deploy one file to each server's index.html location.
4	Set up Nginx on the third server. It will act as a load balancer.
5	Configure Nginx to load and balance traffic between two static websites.
6	Add the Nginx Load balancer IP to the DNS A record.
7	Try accessing the website. Every time you reload the website you should see a different index.html.

**Documentation**

- Spin up your 3 ubuntu servers. Make sure they are clearly labeled

![1](img/1.png)

## Install Nginx and Setup Your Website

- Download your website template from your preferred website by navigating to the website, locating the template you want

![2](img/2.png)

- Right click and select Inspect from the drop down menu.

- Click on the Network tab.

- Click the Download button and right click on the website name.

- Select Copy and click on Copy URL.

![3](img/3.png)

- To install Nginx, execute the following commands on your terminal.

           sudo apt update

           sudo apt upgrade

           sudo apt install nginx

- Start your Nginx server by running the sudo systemctl start nginx command, enable it to start on boot by executing sudo systemctl enable nginx, and then confirm if it's running with the sudo systemctl status nginx command.

![4](img/4.png)

> [!NOTE]

Install Nginx on both web server terminals. These are the terminals you're using to manage the servers hosting the two distinct website contents that the load balancer will distribute traffic to.

- Visit your instances IP address in a web browser to view the default Nginx startup page.

Execute sudo apt install unzip to install the unzip tool and run the following command to download and unzip your website files sudo curl -o /var/www/html/2137_barista_cafe.zip https://www.tooplate.com/zip-templates/2137_barista_cafe.zip && sudo unzip -d /var/www/html/ /var/www/html/2137_barista_cafe.zip && sudo rm -f /var/www/html/22137_barista_cafe.zip

![6](img/6.png)

- To set up your website's configuration, start by creating a new file in the Nginx sites-available directory. Use the following command to open a blank file in a text editor: sudo nano /etc/nginx/sites-available/cafe.

- Copy and paste the following code into the open text editor.

server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

![7](img/8.png)

- Edit the root directive within your server block to point to the directory where your downloaded website content is stored.

- Create a symbolic link for both websites by running the following command. sudo ln -s /etc/nginx/sites-available/cafe /etc/nginx/sites-enabled/

- Run the sudo nginx -t command to check the syntax of the Nginx configuration file, and when successful run the sudo systemctl restart nginx command.

![8](img/7.png)

- Repeat the process for the second website.

![9](img/9.png)

- Execute sudo apt install unzip to install the unzip tool and run the following command to download and unzip your website files sudo curl -o /var/www/html/2134_gotto_job.zip https://www.tooplate.com/zip-templates/134_gotto_job.zip && sudo unzip -d /var/www/html/ /var/www/html/2134_gotto_job.zip && sudo rm -f /var/www/html/2134_gotto_job.zip

![1](img/10.png)

- To set up your website's configuration, start by creating a new file in the Nginx sites-available directory. Use the following command to open a blank file in a text editor: sudo nano /etc/nginx/sites-available/job

- Copy and [paste the following code into the open text editor.

server {
    listen 80;
    server_name placeholder.com www.placeholder.com;

    root /var/www/html/placeholder.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}]

- Edit the root directive within your server block to point to the directory where your downloaded website content is stored.

- Create a symbolic link for both websites by running the following command. sudo ln -s /etc/nginx/sites-available/job /etc/nginx/sites-enabled

- Run the sudo nginx -t command to check the syntax of the Nginx configuration file, and when successful run the sudo systemctl restart nginx command.

![12](img/12.png)

> [!NOTE]

On your first server, run sudo rm /etc/nginx/sites-enabled/default, and on your second server, run sudo rm /etc/nginx/sites-enabled/default. This will delete the default site-enabled folders and enable Nginx to serve content from your specified website directories. If you don't delete these default folders, you'll continue to see the default Nginx page.

![13](IMG/13.png)

- Run the sudo systemctl restart nginx command to restart your server.

- Check both IP addresses to confirm your website is up and running

![14](IMG/14.png)

![15](IMG/15.png)

## Configure your load balancer

- Install Nginx on the server you want to use as a load balancer, and execute sudo systemctl status nginx to ensure it's running.

![16](img/16.png)

- Execute sudo nano /etc/nginx/nginx.conf to edit your Nginx configuration file.

- Add the following within the http block.


    upstream johnsonakin {
    server 1 172.31.81.135;  
    server 2 172.31.94.58;
    Add more servers as needed
}

server {
    listen 80;
    server_name johnsonakin.ca www.johnsonakin.ca;

    location / {
        proxy_pass http://johnsonakin;
    }
}

![17](img/17.png)

- Run sudo nginx -t to check for syntax error.

![18](img/18.png)

- Apply the changes by restarting Nginx: sudo systemctl restart nginx

## Create an A Record

- Point your domain's DNS records to the IP addresses of your Nginx load balancer server.

- In route 53, select the domain name and click on Create record.

![19](img/19.png)

- Paste your IP address➀ and then click on Create records➁ to create the root domain.

![20](img/20.png)

- Click on create record again, to create the record for your sub domain.

- Pasted my IP address, input the Record name(www) and then click on Create records.

- Go to the terminal you used in setting your first website and run sudo nano /etc/nginx/sites-available/cafe to edit your settings. Enter the name of your domain and then save your settings.

- Restart your nginx server by running the sudo systemctl restart nginx command.

- Go to the terminal you used in setting your second website and run sudo nano /etc/nginx/sites-available/job to edit your settings. Enter the name of your domain and then save your settings.

- Restart your nginx server by running the sudo systemctl restart nginx command.

- Go to your domain name in a web browser to verify that your website is accessible.

![21](img/21.png)

![22](img/22.png)

- Reload the webpage to ensure the load balancer distributes traffic evenly between your servers.

## Install certbot and Request For an SSL/TLS Certificate

- Install certbot by executing the following commands: sudo apt update sudo apt install python3-certbot-nginx

- Execute the sudo certbot --nginx command to request your certificate. Follow the instructions provided by certbot and select the domain name for which you would like to activate HTTPS

- You should get a congratulatory message that says https has been successfully enabled.

- Access your website to verify that Certbot has successfully enabled HTTPS

- It is recommended to renew your LetsEncrypt certificate at least every 60 days or more frequently. You can test renewal command in dry-run mode: sudo certbot renew --dry-run


