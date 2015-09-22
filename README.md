# django skeleton project for deploy in digitalocean

1. Clone the repository on you local environment
2. Create a Digital ocean account if you don't have one
3. Add you ssh key to your digital ocean account

   ````
    $ pbcopy < ~/.ssh/id_rsa.pub
   ````
4. Paste your ssh into digital ocean

## Create Digital Ocean Droplet
1. Give it name
2. Select your droplet size
3. Select Region
4. Select application in this case Django on 14.04
5. select your ssh key

### Setup your droplet
1. connect to you droplet via ssh
   ````
    $ssh root@<droplet IP address> (use your droplet IP address)
   ````
2. Capture the DB password and *update your settings* (when you log in you can see this information in the terminal)
3. Then drop and create the existing database
   ```
    $ sudo su - postgres
    $ dropdb django
    $ createdb django
    $ exit
   ```
4. Setup the git repo
    ````
    $ apt-get install git
    $ cd /home/django
    $ mkdir repo
    $ cd repo
    $ git init --bare
    $ cd hooks
    ````
5. Create or edit your post-receive with the script below
    ````
    #!/bin/sh
    git --work-tree=/home/django/django_project --git-dir=/home/django/repo checkout -f

    pip install -r /home/django/django_project/requirements.txt

    python /home/django/django_project/manage.py migrate --noinput

    service gunicorn restart
    ````
6. Make the hook executable
    ````
    $ chmod +x post-receive
    ````
7. Add production remote to local environment
    ````
    $ git remote add production ssh://root@<Droplet Ip adrees>/home/django/repo
    ````
8. Push code to new production remote
    ````
    $ git push production master
    `````
    - if that fails, use this
        ````
        $ git push production master --force
        ````

## Create your superuser
1. Connected via ssh
 ```
  ssh root@<droplet IP address> (use your droplet IP address)
  ```
2. Go into the project directory
  ```
   cd /home/django/django-project/
  ./manage.py loaddata fixtures/user.json
  ```
3. Go to the browser
    ````
    your.ip.adress/admin
    ````
    User: admin
    Password: admin