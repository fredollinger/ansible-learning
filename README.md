# Setting Up Ansible With Docker Tutorial

## Introduction

How to automate Docker containers with ansible.

Assumes that you have Docker and Ansible installed and know the basics of Docker and
ssh. Rake knowledge is helpful, but probably not needed.

It's also assuming that this is running under a recent Ubuntu, but it will probably work with 
other Linux distros with minor changes.

## Install Packages

Just make sure you install rake, ansible, and docker first.

```
  $ sudo apt install ansible docker rake
```

Here's a tutorial on getting started with Docker and ssh: 

https://phoenixnap.com/kb/how-to-ssh-into-docker-container

Here's a github of scripts I wrote to get started:

# Set up Docker Images

## Create Docker Images

You can create Docker images:

```
  $ rake create
```

Check to ensure that they exist:

```
  $ docker ps
```
Should be running here:

```
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                   NAMES
640a457f86a9        xenial_sshd         "/usr/sbin/sshd -D"   3 hours ago         Up 3 hours          0.0.0.0:32770->22/tcp   ssh3
a5f98fa00fe9        xenial_sshd         "/usr/sbin/sshd -D"   3 hours ago         Up 3 hours          0.0.0.0:32769->22/tcp   ssh2
3704bd30fadb        xenial_sshd         "/usr/sbin/sshd -D"   3 hours ago         Up 3 hours          0.0.0.0:32768->22/tcp   ssh1
```
Note your output might be slightly different, but the most important thing is
that you need to ensure that there are 3 images called: ssh1, ssh2, and ssh3.

## Set up /etc/hosts File

Next, get the ip addresses of the Docker images and put them into the hosts file:

```
  $ rake hosts
```

This will create a local hosts file which you should inspect to ensure that it's what you want. It copies /etc/hosts to the local directory and appends the docker image IPs onto the end.

Careful with the next command which will make the hosts aliases usable:

```
  $ sudo mv etc-hosts /etc/hosts
```

## Copy SSH Keys To Docker Images

Note: We need to ensure that we have an ssh key created:

https://confluence.atlassian.com/bitbucketserver/creating-ssh-keys-776639788.html

```
  $ ssh-keygen -t rsa
```

# Setup Ansible

## Add Docker Images to our Ansible Inventory

Ansible inventory is the list of machines that Ansible controls and lives in /etc/ansible/hosts by default.

We can re-run our command to ensure we have a hostsfile for Ansible.

```
  $ rake hosts
```

Check the resulting ansible-hosts file to ensure that it looks good so that we don't lose any
changes we made in the past.

When we are okay with this file, we can deploy it:

```
  $ sudo mv ansible-hosts /etc/ansible/hosts
```

## Ensure We Use the Root User 

By default, ansible will use the local user as the ssh username. This will fail in our example unless
the user logs in as root which is not recommended. To make things work for all users do this edit the
ansible config file:

```
  $ sudo vim /etc/ansible/ansible.cfg
```
Find this line and uncomment it like so:

```
remote_user = root 
```

NOTE: Comments are leading pound signs (#).

## Test If Ansible Is Working

Finally, you should be up and running. Use the ping test to ensure success:

```
  $ ansible all -m ping 
```

Success:

```
ssh1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
ssh2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
ssh3 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```