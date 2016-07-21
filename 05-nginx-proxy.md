# Nginx Config

In the last page, we set up Gunicorn to run mailman-web. But the port has been set to 80, not user-friendly. So we need to do some magic here -- proxy it using nginx.

## Install nginx

Run it under ubuntu user (not mailman):

    sudo apt-get install nginx

Visit http://[DOMAIN]/, you should see a **Welcome to nginx!** page. Go to next step. Else, Google your error.

## Configure nginx

Create a new config in /etc/nginx/sites-available/[YOUR SITE DOMAIN].conf with:

    server {

        listen   80;
        server_name [DOMAIN YOU USE];

        location /static/ {
            root /var/spool/mailman-web/;
        }

        location / {
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-NginX-Proxy true;
            proxy_pass http://127.0.0.1:8000/;
            proxy_redirect off;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }

        # Error pages
        error_page 500 502 503 504 /500.html;
        location = /500.html {
            root /var/spool/mailman-web/;
        }
    }

Then make softlink:
    
    sudo ln -s /etc/nginx/sites-available/[YOUR SITE DOMAIN].conf /etc/nginx/sites-enabled/[YOUR SITE DOMAIN].conf

Restart nginx

    sudo service nginx restart
    
Test it! Have Fun!

For more information, please refer to mailman documentation.
