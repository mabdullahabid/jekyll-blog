---
layout: post
title: The fastest way to install Docker on Linux
---

### Docker: Convenience Script
This is a pretty simple guide to getting Docker and Docker-Compose setup on your server. Most of the information here is taken directly from Docker’s documentation and if you’ve played around with Docker then you’ll probably find this pretty beginner-level.

However, I found myself having to install docker and docker-compose quite so often on many of the fresh installs of servers that I did. Of course, you can find a pre-built AMI that already includes all this but I prefer having to do it myself, just so I know there is no extra bloatware on the server. I’m installing this on an **Ubuntu Server 20.04 LTS**.

So here goes…

### Step 1
Download the convenience script for the latest release of Docker CE on Linux from get.docker.com

```curl -fsSL https://get.docker.com -o get-docker.sh```

This will download the get-docker.sh script which installs docker and configures everything for you.

```sudo sh get-docker.sh```

After this script finishes running, I always run a test docker command just to see if it’s working or not.

```docker --version```

By default, your current user does not always get access to run the Docker commands. If you want to use Docker as a non-root user, then you need to add your user to the Docker group.

```sudo usermod -aG docker $USER```

After running this, don’t forget to log out of the server and log back in for this to take effect.

### Step 2
The next step is to configure Docker to start on boot. As most Docker deployments, whether they are used in staging or production, require that all the microservices are automatically run on server boot. We need to have Docker running as a daemon for that to happen.

```sudo systemctl enable docker```

### Step 3
Now we need to download and make use of Docker Compose.

```sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose```

This is pretty straight-forward as it does not require installing or running any additional commands except giving the docker-compose appropriate permissions. At the time of writing this article, the version was **1.27.4**, please confirm that you are downloading the latest version.

We also want to give docker-compose permissions to be an executable binary

```sudo chmod +x /usr/local/bin/docker-compose```

The last and final step is to test that docker-compose has been successfully downloaded and it works.

```docker-compose --version```

This should show you the version of your docker-compose binary.

That’s it! This is a pretty straight-forward process, and if you’re familiar with docker then you know this too well but I just thought that I’ll share my workflow with others so they can save some time.

### Launch Script
Another way to do this would be to add the code to a launch script which is run at the time of instance initialization. Something like:

```#!/bin/bash
curl -fsSL https://get.docker.com -o get-docker.sh  
sudo sh get-docker.sh  
sudo usermod -aG docker ubuntu  
sudo systemctl enable docker  
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose  
sudo chmod +x /usr/local/bin/docker-compose  
```
