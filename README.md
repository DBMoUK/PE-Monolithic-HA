# PE-Monolithic-HA

#### Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Size and Limitations](#size-and-limitations)
4. [Installation and Puppet Master](#installation-and-puppet-master)
    * [Quickstart](#quickstart)
    * [Clone the Repository](#clone-the-repository)
    * [Configure the Puppet Master](#configure-the-puppet-master)
    * [Access the Puppet Enterprise Console](#access-the-puppet-enterprise-console)
6. [Shutting Down and Resuming](#shutting-down-and-resuming)

## Overview
This is a modified version of seteam environment created by TSE that allows for two monolithic masters, active and passive, and two agents depending on active.

The most up to date instructions will always be here: [TSE Confluence Demo Environment Page](https://confluence.puppetlabs.com/display/TSE/Demo+Environment)

## Prerequisites
This tool is built on top of a few different technologies, mainly VirtualBox and Vagrant, so you'll need to ensure that those are present before you continue. You'll also need to have the Git tools installed to checkout the repository. 

1. Install [Virtual Box](https://www.virtualbox.org/wiki/Downloads).
2. Install [Vagrant](http://vagrantup.com/).
3. Install the required Vagrant plugins:
	* `$ vagrant plugin install oscar`
	* `$ vagrant plugin install vagrant-hosts`
	* `$ vagrant plugin install vagrant-multiprovider-snap` (optional, but you won't have snapshot functionality if you don't install it)

Note that you'll also need acceess to the [private repository on GitHub](http://www.github.com/puppetlabs/seteam-vagrant-stack). If you don't have access for some reason, go ahead and open a ticket on [Jira](https://jira.puppetlabs.com) to let Operations know to give you access. 

## Size and Limitations
Each new VM takes up about 1.7GB to 7GB of hard drive space (depending on the image type), and each snapshot takes up about half as much as a full image. If you no longer need a particular VM and want to free up that space, use `vagrant destroy {vm name}`. If that doesn't work for some reason, you can manually delete the VM images and snapshots from `~/VirtualBox VMs/`.

The VMs also use a significant amount of RAM while they're running—about 1GB each—as well as CPU time. Most relatively recent laptops should be able to run at least 3-5 VMs while maintaining decent performance, but your mileage may vary. Make sure to shut down VMs when not in use (see [Shutting Down and Resuming](#shutting-down-and-resuming)) to avoid slowing down your computer unnecessarily. 


## Installation and Puppet Master
### Quickstart

    vagrant destroy -f; vagrant up; vagrant provision --provision-with hosts; vagrant snap take

### Clone the repository
First you'll need to clone this repository from GitHub. If the following command doesn't work then you should refer to the above note about access to the Puppet Labs private repositories.

	$ git clone git@github.com:LMacchi/PE-Monolithic-HA.git

### Configure the Puppet Master
You're finally ready to begin creating your demonstration environment. Change into the `seteam-vagrant-stack` directory and bring up the puppet master:

	$ cd PE-Monolithic-HA
	$ vagrant up

  By default, this will start all VMs listed in PE-Monolithic-HA/config/vms.yaml

  To start individual VMs, such as the Active / Passive Master pair:

  $ vagrant up active.inf.puppetlabs.demo
  $ vagrant up passive.inf.puppetlabs.demo

It's going to take a little while to download the Centos image and configure it, during which time you'll see a lot of activity in the terminal. When it finishes, you should see something like this:

	Notice: /Stage[main]/Role::Puppetmaster/Exec[instantiate_environment]/returns: executed successfully
	Notice: Finished catalog run in 379.72 seconds

You may also see a warning that "The guest additions on this VM do not match the installed version of
VirtualBox." That doesn't appear to impact the functionality and it should be fixed with a subsequent release of the seteam-vagrant-stack tool.

### Access the Puppet Enterprise Console
You'll be back at the command prompt, but the puppet master is still running in the background. Before you can get to the console, you'll need to figure out where it is:

	$ vagrant hosts list

The response should look something like `10.20.1.1 master`, meaning that the `master` VM has the IP address of `10.20.1.1`. Next, just point your browser to `https://10.20.1.1` (or whatever the actual IP address is) and log in with the username `admin@puppetlabs.com`, password `puppetlabs`. Don't worry if you get a warning about the security certificate; that really won't affect anything. 


## Shutting Down and Resuming
After a long day of booting up virtual machines in Vagrant, the output of `vagrant status` may start to look something like this:

	Current machine states:

	master                    running (virtualbox)
	centos59a                 not created (virtualbox)
	centos64a                 not created (virtualbox)
	centos64b                 running (virtualbox)
	centos64c                 not created (virtualbox)
	debian607a                running (virtualbox)
	ubuntu1204a               running (virtualbox)
	sles11a                   not created (virtualbox)
	solaris10a                running (virtualbox)
	server2008r2a             not created (virtualbox)
	server2008r2b             not created (virtualbox)
	
Running that many virtual machines is going to make it difficult to get much else done on your computer, so you'll want to run the following command to suspend all of the running virtual machines and reclaim those resources:

	$ vagrant suspend

You can also suspend individual machines by adding their name to the end of the command (e.g., `vagrant suspend master`). You can resume all suspended VMs with a single command, `vagrant resume`, or resume one at a time by specifying the name (e.g., `vagrant resume master`).

## TO DO
Uff, lots. 
- This environment has been created to test `puppetlabs/prosvc-pe_ha`.
Stdlib and epel are requirements, that hasn't been automated yet. 
- Both active and passive need to be classified as master in the active console.
- Add CNAME `puppet` to active box.

