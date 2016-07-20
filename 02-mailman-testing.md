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

## Start your test server

Start mailman:

    ./bin/mailman start

Run Django (the web interfaces server):

    ./bin/mailman-web-django-admin runserver 0.0.0.0:8000

Now, the web interface are available at:
- http://[IP OR DOMAIN}:8000/mailman3 for Postorius
- http://[IP OR DOMAIN}:8000/archives for HyperKitty
