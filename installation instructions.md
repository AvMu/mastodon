![Mastodon](https://i.imgur.com/NhZc40l.png)
========

[![GitHub release](https://img.shields.io/github/release/tootsuite/mastodon.svg)][releases]
[![Build Status](https://img.shields.io/circleci/project/github/tootsuite/mastodon.svg)][circleci]
[![Code Climate](https://img.shields.io/codeclimate/maintainability/tootsuite/mastodon.svg)][code_climate]
[![Translation status](https://weblate.joinmastodon.org/widgets/mastodon/-/svg-badge.svg)][weblate]
[![Docker Pulls](https://img.shields.io/docker/pulls/tootsuite/mastodon.svg)][docker]

[releases]: https://github.com/tootsuite/mastodon/releases
[circleci]: https://circleci.com/gh/tootsuite/mastodon
[code_climate]: https://codeclimate.com/github/tootsuite/mastodon
[weblate]: https://weblate.joinmastodon.org/engage/mastodon/
[docker]: https://hub.docker.com/r/tootsuite/mastodon/

Mastodon is a **free, open-source social network server** based on ActivityPub. Follow friends and discover new ones. Publish anything you want: links, pictures, text, video. All servers of Mastodon are interoperable as a federated network, i.e. users on one server can seamlessly communicate with users from another one. This includes non-Mastodon software that also implements ActivityPub!

Click below to **learn more** in a video:

[![Screenshot](https://blog.joinmastodon.org/2018/06/why-activitypub-is-the-future/ezgif-2-60f1b00403.gif)][youtube_demo]

[youtube_demo]: https://www.youtube.com/watch?v=IPSbNdBmWKE

## Navigation

- [Project homepage 🐘](https://joinmastodon.org)
- [Support the development via Patreon][patreon]
- [View sponsors](https://joinmastodon.org/sponsors)
- [Blog](https://blog.joinmastodon.org)
- [Documentation](https://docs.joinmastodon.org)
- [Browse Mastodon servers](https://joinmastodon.org/#getting-started)
- [Browse Mastodon apps](https://joinmastodon.org/apps)

[patreon]: https://www.patreon.com/mastodon

## Features

<img src="https://docs.joinmastodon.org/elephant.svg" align="right" width="30%" />

**No vendor lock-in: Fully interoperable with any conforming platform**

It doesn't have to be Mastodon, whatever implements ActivityPub is part of the social network! [Learn more](https://blog.joinmastodon.org/2018/06/why-activitypub-is-the-future/)

**Real-time, chronological timeline updates**

See the updates of people you're following appear in real-time in the UI via WebSockets. There's a firehose view as well!

**Media attachments like images and short videos**

Upload and view images and WebM/MP4 videos attached to the updates. Videos with no audio track are treated like GIFs; normal videos are looped - like vines!

**Safety and moderation tools**

Private posts, locked accounts, phrase filtering, muting, blocking and all sorts of other features, along with a reporting and moderation system. [Learn more](https://blog.joinmastodon.org/2018/07/cage-the-mastodon/)

**OAuth2 and a straightforward REST API**

Mastodon acts as an OAuth2 provider so 3rd party apps can use the REST and Streaming APIs, resulting in a rich app ecosystem with a lot of choice!

## Deployment

**Tech stack:**

- **Ruby on Rails** powers the REST API and other web pages
- **React.js** and Redux are used for the dynamic parts of the interface
- **Node.js** powers the streaming API

**Requirements:**

- **PostgreSQL** 9.5+
- **Redis**
- **Ruby** 2.4+
- **Node.js** 8+

The repository includes deployment configurations for **Docker and docker-compose**, but also a few specific platforms like **Heroku**, **Scalingo**, and **Nanobox**. The [**stand-alone** installation guide](https://docs.joinmastodon.org/administration/installation/) is available in the documentation.

A **Vagrant** configuration is included for development purposes.

## Contributing

Mastodon is **free, open source software** licensed under **AGPLv3**.

You can open issues for bugs you've found or features you think are missing. You can also submit pull requests to this repository, or submit translations using Weblate. To get started, take a look at [CONTRIBUTING.md](CONTRIBUTING.md). If your contributions are accepted into Mastodon, you can request to be paid through [our OpenCollective](https://opencollective.com/mastodon).

**IRC channel**: #mastodon on irc.freenode.net

## License

Copyright (C) 2016-2019 Eugen Rochko & other Mastodon contributors (see [AUTHORS.md](AUTHORS.md))

This program is free software: you can redistribute it and/or modify it under the terms of the GNU Affero General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License along with this program. If not, see <https://www.gnu.org/licenses/>.



## AWS - docker - ubuntu 18.04

**Overview**
Below are the step by step instructions for getting a mastodon instance running smoothly on AWS using Docker. 

**Pre-requisites**
- A. AWS t2 small instance
- B. AWS S3 bucket
- C. relevant user groups and keys for accessing intra AWS services
- D. domain name with valid SSL
  
**Modus operandi**

- A. 
  1. As you login to AWS console from any browser, you can search / navigate to EC2.
  2. If you want to run without incurring much cost, t2micro would be ideal. However, even after swap memory allocation, the installation fails at precompiling assets. Hence, t2small is preferred for smooth and error free installation.
  3. Tried with free tier Linux AMI, but the installation would stuck up to some random unknown error. Hence, preferred choice would be latest stable version of ubuntu available from list.
  4. once you review and launch, you can generate and download privKey.pem 
  5. check the inbound rules for this EC2 instance by clicking launch wizard besides [inbound rules](amazon.com). Free up SSH, HTTP and HTTPS with ports 80 and 443 respectively
  6. once the rules are in place, set up the elastic IP ( paid service ) or use the public IP for SSH
  7. open command prompt / power shell OR terminal and ssh to this instance using `ssh -i privKey.pem ubuntu@your_public_IP_here`
  
we begin by setting up dependencies prior to installation of docker and mastodon. 

```
sudo -i
sudo apt-get update
sudo apt install nano

nano /etc/ssh/sshd_config
``` 
Add these to your file OR set the following values after uncommenting

>ClientAliveInterval 120

>ClientAliveCountMax 720

#ctrl+x, y, enter

`service sshd reload`

All dependencies need to be installed as root

`
sudo apt-get install -y apt-transport-https software-properties-common ca-certificates curl gnupg-agent 
`

And remove all previous installations of docker. you can come back here if you want to start over ;) 

Don't stress if below commands throw an error like package/file doesn't exist :D
```
sudo apt-get remove -y docker docker-engine docker.io
sudo apt-get -y remove docker docker-engine docker.io containerd runc
sudo apt-get purge docker-ce
sudo rm /usr/local/bin/docker-compose
pip uninstall docker-compose
```
Images, containers, volumes, or customized configuration files on your host are not automatically removed. To delete all images, containers, and volumes:
`sudo rm -rf /var/lib/docker`

--> There are two options for escaping the **out of memory** error while precompiling assets
   1. Swap partition

```
test
```

   2. Swap file
```
sudo fallocate -l 2G /swapfile 
#if fallocate fails --> sudo dd if=/dev/zero of=/swapfile bs=1024 count=1048576
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo nano /etc/fstab
```
Add the following line to file fstab # use ctrl+end for reaching the last line in file

> /swapfile swap swap defaults 0 0 

#ctrl+x, y, enter
  
#to remove the swap --> `sudo swapoff /mnt/swapfile && sudo rm /mnt/swapfile`

 _Install the docker_ [Official steps](https://docs.docker.com/v17.09/docker-for-aws/#quickstart) sourced from [here](https://docs.docker.com/install/linux/docker-ce/ubuntu/) and for [docker swarm](https://docs.docker.com/v17.12/docker-cloud/cloud-swarm/link-aws-swarm/)
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
```
#install latest --> `sudo apt-get install docker-ce docker-ce-cli containerd.io`

OR

choose your own basis the current machine OS release

` lsb_release -a 

apt-cache madison docker-ce
 `

syntax --> sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

e.g. `sudo apt-get install docker-ce=5:18.09.4~3-0~ubuntu-bionic docker-ce-cli=5:18.09.4~3-0~ubuntu-bionic containerd.io`

If below command gives you error, you should restart the instance from AWS console and re-login to root. Check the docker [post installation guide](https://docs.docker.com/install/linux/linux-postinstall/)

`docker info
sudo service docker status`

If you get error, **starting daemon** : Devices cgroup isn't mounted

`sudo cgroupfs-mount
sudo service docker start`

else you are good to go ahead and use systemd to manage which services start when the system boots. Ubuntu 14.10 and below use upstart. 

`sudo systemctl enable docker`

 _Install the docker-compose_    [official page here](https://docs.docker.com/compose/install/)
```
sudo apt-get install -y py-pip python-dev libffi-dev openssl-dev gcc libc-dev make
#sudo yum install git # for EC2 custom Linux AMI
sudo curl -o /usr/local/bin/docker-compose -L "https://github.com/docker/compose/releases/download/1.18.0/docker-compose-$(uname -s)-$(uname -m)"

sudo chmod +x /usr/local/bin/docker-compose
docker-compose -v
docker -v
systemctl daemon-reload
systemctl start docker
```
 _Install the mastodon_    [official instructions here](https://github.com/tootsuite/documentation/blob/master/Running-Mastodon/Docker-Guide.md)

```
adduser mastodon
passwd mastodon
usermod -aG sudo mastodon
#usermod -aG wheel mastodon # used in EC2 custom Linux 2 AMI for adding  user to sudo group
usermod -aG docker mastodon
chown mastodon:mastodon /home/mastodon/.docker/ -R
su - mastodon
sudo chmod g+rwx "$HOME/.docker" -R
sudo chmod +x /usr/local/bin/docker-compose

cd /home/mastodon
 git clone https://github.com/tootsuite/mastodon.git live
 cd live
  git tag -l 
  git checkout tags/v1.4.7 # replace 1.4.7 with latest available from list
  #git checkout $(git tag -l | grep -v 'rc[0-9]*$' | sort -V | tail -n 1)
  cp .env.production.sample .env.production
  nano docker-compose.yml 
 ```
Put below in the file 
 ```
 version: '3'
services:

  db:
    restart: always
    image: postgres:9.6-alpine
    networks:
      - internal_network
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres"]
    volumes:
      - ./postgres:/var/lib/postgresql/data

  redis:
    restart: always
    image: redis:5.0-alpine
    networks:
      - internal_network
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
    volumes:
      - ./redis:/data

  web:
#    build: .
    image: tootsuite/mastodon:latest
    restart: always
    env_file: .env.production
    command: bash -c "rm -f /mastodon/tmp/pids/server.pid; bundle exec rails s -p 3000 -b '0.0.0.0'"
    networks:
      - external_network
      - internal_network
    healthcheck:
      test: ["CMD-SHELL", "wget -q --spider --header 'x-forwarded-proto: https' --proxy=off localhost:3000/api/v1/instance || exit 1"]
    ports:
      - "0.0.0.0:3000:3000"
    depends_on:
      - db
      - redis
#      - es
    volumes:
      - ./public/system:/mastodon/public/system
      - ./public/assets:/mastodon/public/assets
      - ./public/packs:/mastodon/public/packs

  streaming:
#    build: .
    image: tootsuite/mastodon:latest
    restart: always
    env_file: .env.production
    command: yarn start
    networks:
      - external_network
      - internal_network
    healthcheck:
      test: ["CMD-SHELL", "wget -q --spider --header 'x-forwarded-proto: https' --proxy=off localhost:4000/api/v1/streaming/health || exit 1"]
    ports:
      - "0.0.0.0:4000:4000"
    depends_on:
      - db
      - redis

  sidekiq:
 #   build: .
    image: tootsuite/mastodon:latest
    restart: always
    env_file: .env.production
    command: bundle exec sidekiq
    depends_on:
      - db
      - redis
    networks:
      - external_network
      - internal_network
    volumes:
      - ./public/system:/mastodon/public/system
      - ./public/packs:/mastodon/public/packs
networks:
  external_network:
  internal_network:
    internal: true
 ```
After saving this file, run `docker-compose build`

Now for editing the .env.production file, we need 5 values. 

Run this three times to get the loonnng code `docker-compose run --rm web bundle exec rake secret`

paste these codes against SECRET_KEY_BASE, OTP_SECRET & PAPERCLIP_SECRET

Run this two times `docker-compose run --rm web bundle exec rake mastodon:webpush:generate_vapid_key`

paste these codes against VAPID_PUBLIC_KEY & VAPID_PUBLIC_KEY.
You can edit the file using nano

You will need to configure the database by entering in postgres docker container
```
docker exec -i -t mastodon_db_1  /bin/bash
 su - postgres
 createuser -P your_username
 createdb your_databasename -O your_username
 createdb mastodon_production -O your_username
\q
```

And finally run these commands to make the server live up and running
```
docker-compose run --rm web rails db:migrate
docker-compose run --rm web rails assets:precompile
docker stop $(docker ps -a -q) && docker-compose up -d
```


 /etc/docker/daemon.json
sudo systemctl enable docker 
 docker run -d -p 80:80 --name nginx nginx
 
 sudo /etc/init.d/nginx restart
 
 
 SMTP_SERVER=mail.plank.life 
SMTP_PORT=465
SMTP_LOGIN=avishkar@plank.life
SMTP_PASSWORD=
SMTP_FROM_ADDRESS=avishkar@plank.life
 
 

- B. 
  1. After you login to AWS console from any browser, you can search / navigate to S3.
  2. Create new bucket
  3. enable static web hosting in properties of bucket and note the endpoint
  4. [sourced from here](https://medium.com/@Keithweaver_/only-allowing-access-to-your-s3-bucket-via-your-website-5ca5c8546152)
  5. generate access key and secret key



