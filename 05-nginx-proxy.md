# Nginx Config

In the last page, we set up Gunicorn to run mailman-web. But the port has been set to 80, not user-friendly. So we need to do some magic here -- proxy it using nginx.

## Install nginx

Run it under ubuntu user (not mailman):

    sudo apt-get install nginx

Visit http://[DOMAIN]/, you should see a **Welcome to nginx!** page. Go to next step. Else, Google your error.

## Configure nginx
.......

    server {
      listen 80;

      server_name forum.example.org;

      location / {
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header Host $http_host;
          proxy_set_header X-NginX-Proxy true;
          proxy_pass http://127.0.0.1:4567/;
          proxy_redirect off;
          # Socket.IO Support
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "upgrade";
      }
    }
