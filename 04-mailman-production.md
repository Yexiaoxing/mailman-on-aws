# Put Mailman into Production

## Install MYSQL

In this instance, we use MySQL to store data from Mailman.

We have already set up our RDS server, let's review it.

    Endpoint: mailman.abcd.ap-northeast-1.rds.amazonaws.com:3306
    Username: mailman
    Password: [mailman-pw]
    DB Name: mailmandb
    
First, we need to install mysqlclient-dev in your system using default user. Run 

    sudo apt-get install libmysqlclient-dev

Then, change back to mailman user and activate the virtual environment.
    
    sudo su mailman
    cd /opt/mailman
    source venv/bin/activate
    
Install MySQL library of python

    pip install MySQL-python
    
## Change status to production

For a production setup, you first need to change the deployment parameter to production in buildout.cfg and run buildout again. It will regenerate the scripts in bin and the contents of the deployment directory. Run

    nano mailman-bundler/buildout.cfg
    # Replace "deployment = testing" with "deployment = production"
    buildout
    
In the bundler directory, open the mailman_web/production.py file, look for the SECRET_KEY parameter and set something random.

## Set Database

### Mailman

settings_local.py


    

    Endpoint: mailman.cchuxgtfrpsv.ap-northeast-1.rds.amazonaws.com:3306
pw mailman:mailman-db
mailmandb

    sudo apt-get install libmysqlclient-dev
    pip install MySQL-python 
