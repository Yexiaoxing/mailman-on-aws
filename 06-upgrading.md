# Upgrade some components to the newest version

Mailman-bundler is a convenient tool -- but some of the shipped components are out-of-date. We need to upgrade them.

## Upgrade HyperKitty to git head version

Stop all gunicorn process first.
  
    sudo killall gunicorn

Then make sure you are using `mailman` user, by running `sudo su mailman`

Go to `/opt/mailman` folder, and clone HyperKitty down.

    sudo su mailman
    cd /opt/mailman
    git clone https://gitlab.com/mailman/hyperkitty/
    
Navigate into hyperkitty folder, switch to python2 venv and install.
  
    cd hyperkitty
    source ../venv/bin/activate
    python setup.py install
    

## Upgrade Postorius

Clone the repo and install.

    cd /opt/mailman
    git clone https://gitlab.com/mailman/postorius
    cd postorius
    source ../venv/bin/activate
    python setup.py install
    
## Upgrade mailmanclient (rest api of mailman)

Clone the repo and install.

    cd /opt/mailman
    git clone https://gitlab.com/mailman/mailmanclient
    cd mailmanclient
    source ../venv/bin/activate
    python setup.py install

## Make the upgrade works
    
Replace the original gunicorn startup file.
    
    rm /opt/mailman/mailman-bundler/bin/gunicorn
    nano  /opt/mailman/mailman-bundler/bin/gunicorn

Paste the following file to it.

    #!/opt/mailman/venv/bin/python

    import sys
    sys.path[0:0] = ['/opt/mailman/mailman-bundler',
      '/opt/mailman/venv/lib/python2.7/site-packages',
      '/opt/mailman/venv/local/lib/python2.7/site-packages/',
      '/opt/mailman/venv/lib/python2.7/site-packages/',
      '/opt/mailman/venv/lib/python2.7',
      '/opt/mailman/venv/lib/python2.7/plat-x86_64-linux-gnu',
      '/opt/mailman/venv/lib/python2.7/lib-tk',
      '/opt/mailman/venv/lib/python2.7/lib-old',
      '/opt/mailman/venv/lib/python2.7/lib-dynload',
      '/opt/mailman/mailman-bundler/eggs',
      '/opt/mailman/mailman-bundler/bin',
      '/usr/lib/python2.7',
      '/usr/lib/python2.7/plat-x86_64-linux-gnu',
      '/usr/lib/python2.7/lib-tk']
      
    from os import listdir
    from os.path import join as path_join
    path = '/opt/mailman/mailman-bundler/eggs'
    pathList = listdir(path)
    for i, item in enumerate(pathList):
        sys.path.append(path_join(path, item))
      
    import os
    os.environ['DJANGO_SETTINGS_MODULE'] = 'mailman_web.production'

    import gunicorn.app.wsgiapp

    if __name__ == '__main__':
        sys.exit(gunicorn.app.wsgiapp.run())

Update mailman-web-django-admin file

    rm /opt/mailman/mailman-bundler/bin/mailman-web-django-admin
    nano /opt/mailman/mailman-bundler/bin/mailman-web-django-admin
    
Paste it:

    #!/opt/mailman/venv/bin/python

    import sys
    sys.path[0:0] = ['/opt/mailman/mailman-bundler',
      '/opt/mailman/venv/lib/python2.7/site-packages',
      '/opt/mailman/venv/local/lib/python2.7/site-packages/',
      '/opt/mailman/venv/lib/python2.7/site-packages/',
      '/opt/mailman/venv/lib/python2.7',
      '/opt/mailman/venv/lib/python2.7/plat-x86_64-linux-gnu',
      '/opt/mailman/venv/lib/python2.7/lib-tk',
      '/opt/mailman/venv/lib/python2.7/lib-old',
      '/opt/mailman/venv/lib/python2.7/lib-dynload',
      '/opt/mailman/mailman-bundler/eggs',
      '/opt/mailman/mailman-bundler/bin',
      '/usr/lib/python2.7',
      '/usr/lib/python2.7/plat-x86_64-linux-gnu',
      '/usr/lib/python2.7/lib-tk']
      
    from os import listdir
    from os.path import join as path_join
    path = '/opt/mailman/mailman-bundler/eggs'
    pathList = listdir(path)
    for i, item in enumerate(pathList):
        sys.path.append(path_join(path, item))
    import djangorecipe.binscripts

    if __name__ == '__main__':
        sys.exit(djangorecipe.binscripts.manage('mailman_web.production'))



Then give run permission to it.

    chmod 777 /opt/mailman/mailman-bundler/bin/gunicorn /opt/mailman/mailman-bundler/bin/mailman-web-django-admin 
    chmod -R 7755 /opt/mailman/venv/lib/python2.7/site-packages/ /opt/mailman/venv/local/lib/python2.7/site-packages/

Upgrade the database and cache

    /opt/mailman/mailman-bundler/bin/mailman-web-django-admin makemigrations
    /opt/mailman/mailman-bundler/bin/mailman-web-django-admin migrate
    /opt/mailman/mailman-bundler/bin/mailman-web-django-admin collectstatic # press enter to continue

Fix some permission error

    exit
    sudo chown -R mailman:users /opt/mailman/mailman-bundler/var/mailman-web/
    sudo su mailman
    
Restart gunicorn

    /opt/mailman/mailman-bundler/bin/gunicorn -c /opt/mailman/mailman-bundler/deployment/gunicorn.conf mailman_web.wsgi:application &
