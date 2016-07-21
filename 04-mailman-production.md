# Put Mailman into Production

## Install PostgreSQL

In this instance, we use PostgreSQL to store data from Mailman.

We have already set up our RDS server, let's review it.

    Endpoint: mailman-postgres.abcd.ap-northeast-1.rds.amazonaws.com:5432
    Username: mailman
    Password: [mailman-pw]
    DB Name: mailmandb
    
First, we need to install libpq-dev in your system using default user. Run 

    sudo apt-get install libpq-dev 

Then, change back to mailman user and activate the virtual environment.
    
    sudo su mailman
    cd /opt/mailman
    source venv/bin/activate
    
Install PostgreSQL library of python

    pip install psycopg2

Activate python3 venv:

    source mailman-bundler/venv-3.4/bin/activate
    pip install psycopg2
    source venv/bin/activate
    
## Change status to production

For a production setup, you first need to change the deployment parameter to production in buildout.cfg and run buildout again. It will regenerate the scripts in bin and the contents of the deployment directory. Run

    nano mailman-bundler/buildout.cfg
    # Replace "deployment = testing" with "deployment = production"
    cd mailman-bundler
    buildout
    
In the bundler directory, open the mailman_web/production.py file, look for the SECRET_KEY parameter and set something random.

    nano mailman_web/production.py
    # set SECRET_KEY parameter to something random.
    # here, you may also want to do some modification. Check the things you will need to change here: https://docs.djangoproject.com/en/1.9/ref/settings/

## Set Database

### Mailman

Run:

    nano deployment/mailman.cfg 
    
Uncomment the following lines (delete the # character in the beginning):
    
    #[database]
    #class: mailman.database.postgresql.PostgreSQLDatabase
    #url: postgres://mailman:mailman-db-password@localhost/mailman
    
and replace them with

    [database]
    class: mailman.database.postgresql.PostgreSQLDatabase
    url: postgres://[DB USERNAME]:[DB PASSWORD]@[ENDPOINT]/[DB NAME]

Save.

### Mailman-web

Run:

    nano mailman_web/settings_local.py

Add the following lines:

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'NAME': '[DB NAME]',  # Example, change as needed
            'USER': '[DB USERNAME]',  # Example, change as needed
            'PASSWORD': '[DB PASSWORD]',  # Example, obviously
            'HOST': '[ENDPOINT without port (:5432)]',
            'PORT': '5423',            # Set to empty string for default.
        }
    }
    USE_SSL = False


Save.

## Make Folders for cache

Go back to default user.
    
    exit
    sudo mkdir /var/spool
    sudo mkdir /var/spool/mailman-web
    sudo mkdir /var/spool/mailman-web/static
    sudo chmod 777 /var/spool/mailman-web/ -R
    sudo chown mailman /var/spool/mailman-web -R
    sudo mkdir /var/log/mailman-web/
    sudo chmod 777 /var/log/mailman-web/


## Other settings

There are some other variables that need to be set in production.py for production serving. You must set the **hostname** your website will respond to in **ALLOWED_HOSTS** and **BROWSERID_AUDIENCES**.

Also, you need to append **USE_X_FORWARDED_HOST = True ** to the production.py file.

## Reboot mailman

Run:

    sudo su mailman
    source venv/bin/activate
    cd mailman_bundler
    bin/mailman-post-update

## Running web interface on Gunicorn

To add Gunicorn support, just run the following command:

    buildout install gunicorn

Create a superuser here:

    ./bin/mailman-web-django-admin createsuperuser

You can then serve the Mailman web interfaces with Gunicorn by running the following command:

    ./bin/gunicorn mailman_web.wsgi:application -b 0.0.0.0:8000

No img and css now -- it is normal. 

## Install Mailman Service

You need to run as root or use sudo. So go back to default user. Run:
    
    exit
    sudo cp /opt/mailman/mailman-bundler/deployment/mailman3.service /etc/systemd/system/
    sudo cp /opt/mailman/mailman-bundler/deployment/mailman3.logrotate.conf /etc/logrotate.d
    sudo cp /opt/mailman/mailman-bundler/deployment/mailman-web-gunicorn.service /etc/systemd/system/

Go to configure nginx.
