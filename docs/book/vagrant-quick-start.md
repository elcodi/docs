# Vagrant Quick Start

Bamboo also offer the possibility to try it in a fast way using [Vagrant](https://www.vagrantup.com).

Vagrant is a tool based on [VirtualBox](https://www.virtualbox.org) used to reproduce the same environment and share
it with the team, so if you want start building your next ecommerce from bamboo, the VM will give your team a warranty
about the environment.

Bamboo also uses the [Ansible](http://www.ansible.com/) [provisioner](https://docs.vagrantup.com/v2/provisioning/ansible.html)
to help you configuring the environment correctly, so you don't need to configure it everytime you need to recreate
the virtual machine.

## Requirements

 1. [download](https://www.virtualbox.org/wiki/Downloads) and install VirtualBox
 2. [download](http://www.vagrantup.com/downloads) and install Vagrant
 3. [install](http://docs.ansible.com/ansible/intro_installation.html) Ansible

## Usage

Its usage is simple and is based on three commands:

```
$ cd /path/to/your/bamboo
$ cd vagrant
$ vagrant up
```

The output you'll see is the *provisioning* system, it will give you an "ok" when completed.

If it doesn't finish correctly don't worry, just use the `vagrant provision` to restart
the provisioning process.

Once completed you'll be able to access to your bamboo store at http://10.10.10.10/app_dev.php/

It's not public, 10.10.10.10 is just the address if your local machine.

> If you need to change it, destroy the machine and change the IP in the Vagrantfile.

Well, now that you have your local bamboo store you can work on local files and them will
be synced with the same files on the virtual machine.

When you complete your changes run a

```
$ cd /path/to/your/bamboo
$ cd vagrant
$ vagrant suspend
```

to shut down the virtual machine leaving changes
untouched, and to restart it the day after just do a

```
$ cd /path/to/your/bamboo
$ cd vagrant
$ vagrant resume
```

If you do something wrong with the environment don't worry, it's a virtual machine so won't affect
your local environment, if something goes wrong you can always do a `vagrant destroy` to destroy
the virtual machine and after recreate it using `vagrant up`.

> **Attention:** the vagrant up will take time because recreates everytime the virtual machine and
run the provisioning, so do it only when necessary.

## SSH Access

If you need to access to the environment (for cron jobs, daemon processes, etc...), Vagrant gives
the possibility to access with a simple command:

```
$ cd /path/to/your/bamboo
$ cd vagrant
$ vagrant ssh
```
