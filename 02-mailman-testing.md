# Install Mailman Bundler

Reference: https://gitlab.com/mailman/mailman-bundler

## Install all necessary packages

For Ubuntu or Debian users, run

    sudo apt-get install ruby-sass python python3 python-pip python3-pip git python-virtualenv python-dev python3-dev gcc
    
Centos:

    sudo yum install rubygem-sass python python3 python-pip python3-pip git python-virtualenv python-devel python3-devel gcc

## Setting up for testing first

Prepare an empty folder for mailman:

    sudo mkdir /opt/mailman
    cd /opt/mailman
    
Clone [Mailman Bundler](https://gitlab.com/mailman/mailman-bundler):
    
    sudo git clone https://gitlab.com/mailman/mailman-bundler.git

Now switch to a dedicated Mailman user.

    sudo useradd mailman --no-user-group --create-home
    sudo usermod mailman --append --groups sudo
    sudo chown mailman ./ -R
    sudo su mailman

Setup a virtualenv for the Mailman suite and activate it with the following commands:

    virtualenv venv
    source venv/bin/activate
    
In the bundler directory, open the mailman_web/testing.py file, look for the SECRET_KEY parameter and set something random.
    
    nano mailman-bundler/mailman_web/testing.py
    # SECRET_KEY = '[SET IT TO SOMETHING RANDOM]'

Go into mailman-bundler folder, install and run buildout:

    cd mailman-bundler
    pip install zc.buildout
    buildout

Now initialize Django's database:

    ./bin/mailman-post-update

Now create an initial superuser to login as, this is the user you'll use to login to Mailman's web interface (Postorius):
  
    ./bin/mailman-web-django-admin createsuperuser

Install falcon 0.3.0

    source /opt/mailman/mailman-bundler/venv-3.4/bin/activate
    pip install --upgrade falcon==0.3.0

## Configure Postfix

The deployment/postfix-main.cf contains a few lines that you must add to your main Postfix configuration file (usually /etc/postfix/main.cf).

Exit mailman user and return to default user first.

    exit
    cat mailman-bundler/deployment/postfix-main.cf # Copy all lines shown

The return should look like

    # Support the default VERP delimiter.
    recipient_delimiter = +
    unknown_local_recipient_reject_code = 550
    owner_request_special = no
    # Maps
    transport_maps =
        hash:/opt/mailman/mailman-bundler/var/data/postfix_lmtp
    local_recipient_maps =
        hash:/opt/mailman/mailman-bundler/var/data/postfix_lmtp
    relay_domains =
        hash:/opt/mailman/mailman-bundler/var/data/postfix_domains

Copy them. Then paste it to the postfix config file:

    sudo nano /etc/postfix/main.cf
    sudo postfix reload
    
Make sure postfix has read access to the *.db files in var/data (it won't have access by default). You can change the permissions of those files to world-readable:
    
    sudo chmod 777 mailman-bundler/var/data/

## Start your test server

Change back to mailman user:

    sudo su mailman

Start mailman:

    ./bin/mailman start

Run Django (the web interfaces server):

    ./bin/mailman-web-django-admin runserver 0.0.0.0:8000

Now, the web interface are available at:
- http://[IP OR DOMAIN}:8000/mailman3 for Postorius
- http://[IP OR DOMAIN}:8000/archives for HyperKitty

You should be able to see something but no error. Do some test here.

## Test your server

### Create a domain

- Open your browser and navigate to Postorius (http://127.0.0.1:8000/mailman3)
- Go to Settings -> "New domain"
- Enter a mail host: lists.example.com
- Enter a web host: http://lists.example.com

### Create a mailing-list

- In Postorius, go to "Lists" and click "New list"
- Enter list name, e.g. test
- Choose the mailhost
- Define the list owner (defaults to domain contact)
- Choose whether to advertise list
- Enter description
- Save list.

On the lists' front page, add a moderator in the corresponding category.

### Subscribe to your mailing-list

To subscribe, send an email to test-request@lists.example.com with subject subscribe.
To unsubscribe, send an email to test-request@lists.example.com with subject unsubscribe.
