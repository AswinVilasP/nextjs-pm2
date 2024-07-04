# nextjs-pm2
Deploy Next.js on Ubuntu with PM2

Step 1:
Update your system.

    sudo apt update && sudo apt upgrade

Step 2:
Installing Node.js with apt Using a NodeSource PPA

    cd ~
    curl -sL https://deb.nodesource.com/setup_16.x -o /tmp/nodesource_setup.sh
    nano /tmp/nodesource_setup.sh
    sudo bash /tmp/nodesource_setup.sh
    sudo apt install nodejs
    node -v

Step 3:
Installing Node.js Using the Node Version Manager, always choose LTS version

    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
    source ~/.bashrc
    nvm list-remote
    nvm install v20.15.0
    nvm list

Step 4:
Installing npm

    sudo apt install npm

Step 5:
Installing PM2

    npm install pm2 -g

Step 6:
Install dependencies in Node.js app

    npm install

Step 7:
After installing the dependencies build the project

    npm run build

Step 8:
Configure nginx for the next.js app

    cd /etc/nginx/sites-available
    touch next-app.conf

    server {
            listen 80;
            server_name ubuntu-next-app.abdullahilgaz.com; # !!! - change to your domain name 
          gzip on;
            gzip_proxied any;
            gzip_types application/javascript application/x-javascript text/css text/javascript;
            gzip_comp_level 5;
            gzip_buffers 16 8k;
            gzip_min_length 256;
    
        location /_next/static/ {
                    alias /var/www/next-app/.next/static/; # !!! - change to your app name
                    expires 365d;
                    access_log off;
            }
    
        location / {
                    proxy_pass http://127.0.0.1:3000; # !!! - change to your app port
                    proxy_http_version 1.1;
                    proxy_set_header Upgrade $http_upgrade;
                    proxy_set_header Connection 'upgrade';
                    proxy_set_header Host $host;
                    proxy_cache_bypass $http_upgrade;
            }
    }
    
    sudo ln -s /etc/nginx/sites-available/next-app.conf /etc/nginx/sites-enabled
    sudo nginx -t

Step 9:
Start the next.js app

    pm2 start npm --name "next-app" -- start

Step 10:
Restart nginx server

    sudo systemctl restart nginx

Step 11:
This is an additional step to set the pm2 run on system boot

    pm2 startup systemd

This will generate a startup script for your pm2 process to run by using systemd. you can view the generated script in /etc/systemd/system/ path.
A sample output generated after running the command is given below.

    sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u your_user --hp /home/your_user

    pm2 save
