# ProjectSetup
VortexCommerce/ProjectSetup using docker, with ability to pull in database and media securely and setup projects automatically

# Why

Allows a cross platform setup of all client projects allowing version controlling of the environment it self as well as the codebase using docker, by leveraging the vortex commerce backup system to provide historic as well as current data and media files as well as allowing development on the current stack configuration to minimise any environmental bugs (i.e its the server not me!).

Furthermore allows easy deployment of exact stack to development/staging and (hopefully soon) live sites.

# Authentication
##### To use Vortex Commerce Project Setup you will require access to the following:

1. SSH access to github.com/VortexCommerce
2. SSH access to the backup server

If you do not have any of the above, Ask.

# First time Docker installation

## Mac

First Install Docker for Mac: https://docs.docker.com/docker-for-mac/

Then before following the steps below, Run docker for the first time, Press Next. Click on icon on the top right, make sure it is running!

```
sudo gem install docker-sync
curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
sudo chown -R $(whoami) /usr/local/Cellar && sudo chown -R $(whoami) /usr/local/Homebrew && sudo chown -R $(whoami) /usr/local/var/homebrew/
brew install fswatch
brew install unison
chmod +x /usr/local/bin/docker-compose
```
## Ubuntu

```
sudo apt-get install apt-transport-https ca-certificates
sudo curl -fsSL https://yum.dockerproject.org/gpg | sudo apt-key add -
sudo apt-key fingerprint 58118E89F3A912897C070ADBF76221572C52609D
sudo add-apt-repository \
       "deb https://apt.dockerproject.org/repo/ \
       ubuntu-$(lsb_release -cs) \
       main"
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
sudo apt-get install docker-engine docker
sudo curl -L "https://github.com/docker/compose/releases/download/1.10.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## More Info
If  docker not working using above instructions, refer to this link:
https://docs.docker.com/engine/installation/linux/ubuntu/

# Quick Start

To create a new project called 'TechBuyer' that uses Magento 2 do the following:

Clone this repo using the specific tag: 



Current available tags are as follow:

```
magento-1-php7.1
magento-1-php5.1
magento-2-php7.1
wordpress-latest
```

# Commands

```
usage : ./vortex start|stop|restart|status|stats|magerun|composer|enter|destroy

  start      Starts the docker containers (and triggers the
             installation if magento is not yet installed)
  stop       Stops all docker containers
  restart    Restarts all docker containers
  status     Prints the status of all docker containers
  stats      Displays live resource usage statistics of all containers
  magerun    Executes magerun in the magento root directory
  composer   Executes composer in the magento root directory
  enter      Enters the bash of a given container type (e.g. php, mysql)
  destroy    Stops all containers and removes all data

```

# Docker Containers Available
Visit http://hub.docker.com or http://cloud.docker.com for more information e.g. available tags.

## Public
* percona
* mysql
* redis
* selenium/hub
* selenium/node-firefox
* selenium/node-chrome

## Vortex/Private
* vortexcommerce/php
* vortexcommerce/nginx
* vortexcommerce/varnish
* vortexcommerce/elasticsearch


# FAQ

### Where are the databases and media files stored.

All current sites are backedup up using a custom back up script that in short backups the files every 3 or 6 hours depending on the site and backups all databases every night.

While these backups are incremental to save space, daily backups for the past 30 days are available on the off-site backup server in easily accessible formats (i.e tar,sql).

The files and databases are backed up at random times between 01:00 and 05:00 (randomly upon first deployment).

For the purposes of VortexCommerce/ProjectSetup project, there is always 4 weeks of daily backups accessible while a higher number might be retened on the backup server itself.

The databases are cleansed every morning before placing these on a ssh secured folder by randomising all sensitive data (Except some log tables, other rows are not deleted but names/emails/addresses changed to random values), This allows you to debug specific time bugs by simply rolling back your environment to a specific date.

### How does docker work

Docker in short allow virtualisation by acccessing the kernel directly, This means that processes can be ran securily without requiring their own kernel, So full virtuallisations is not used, thus making docker much leaner than full virtualisation solutins, On linux for example, creating a simple docker image using nginx has less than 5 processes and uses the host's Kernel to run these, on Mac and Windows, a virtualbox machine is used to provide access to a linux kernel but the footprint still remains much lower than fully virtualisation.

### How are the files from my local environment synced to the virtual machcine

On linux these are synced natively however under OSX, those kind of volume shares can become very slow, especially with node-based apps ( a lot of files, magento 2! ). For this pupose, http://docker-sync.io is used to allow a volume-share like synchronisation (by using unison rsync in the background), without using volume shares, this usually speeds up you container read/write speed of the code-directory from 50-80 times, depending on what docker-machine you use.

The Bash Wrapper detects the OS automatically and uses the correct sync method.

### I want to know more, What other packages/softwares/scripts/etc are used to make it all work

1. Docker
2. Docker-Compose
3. Nginx
4. Choice of elasticsearch/solr
5. PHP
6. Redis
7. VortexCommerce/DeploymentTools
8. Bash Script wrapper to bring it all together
