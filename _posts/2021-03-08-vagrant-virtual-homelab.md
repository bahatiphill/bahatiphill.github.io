---
layout: post
title: "Vagrant: virtual homelab"
date: 2021-03-08
featured-image: /assets/images/homelab.jpg
featured-image-alt: Photo by Thomas Jensen on Unsplash
unsplash-image-owner: Thomas Jensen
tags: [homelab, vagrant, ansible]
summary: "Have you ever wanted to try out a new tech tool, but you don't want to install it and all its dependencies on your computer? It makes sense because you will."
permalink: /blog/:title/

---

Have you ever wanted to try out a new tech tool, but you don't want to install it and all its dependencies on your computer? It makes sense because you will only need this in one week or two. There's no need to bloat your PC.

Because you like to keep your computer clean and quick by not installing tools you don't use daily, you end up spinning new tiny servers on one of your favorite cloud providers such as AWS or DigitalOcean to try the tool out. But, most of the time, you forget to destroy those servers until the billing emails come at the end of the month. Then, you have to pay for a service you only used for three days and forget to turn it off. It happened to all of us.

That's where Vagrant gets useful to me. You spin up a virtual machine from the command line, SSH into it, then you have a new fresh environment to install all the packages and tools you wanted to isolate from your computer in a matter of minutes. Even if you spin up more than 10 to 100 Virtual machines and forget to destroy them after you finish all your activities, No one will charge you at the end of the month for using his cloud resources.

## Then, why not using containers [A.K.A Docker]?

Sometimes people will say "docker" when they mean containers and vice versa. Nowadays, Docker is the de facto industry standard and a go-to technology when it comes to the container landscape. But there are other container runtimes to watch for, such as containerd and CRI-O.

Back to the "why not using Docker" question, there are some situations Docker is a better choice, and there are some other situations it doesn't make sense to use Docker.

If you have been using Docker, you know that it is good at environment isolation, plus Docker is lightweight, low overheard than using Virtual machines. It is fast to start a docker container, run there whatever you want, then destroy it. That's quick. Yes, Docker is better when it comes to packaging applications your developer team writes.

But, let's say, as someone in the operation team, you want to set up a production environment. You can define a development/staging environment that is similar to the production. You can try to automate the environment setup process. Vagrant integrates well with provisioners such as Ansible and chef. You can even use shell scripts to provision the Virtual machine after Vagrant has turned it ON.

### Server provisioners?
According to [Wikipedia.](https://en.wikipedia.org/wiki/Provisioning_(telecommunications))
> "Server provisioning is a set of actions to prepare a server with appropriate systems, data, and software, and make it ready for network operation."

Imagine if you're going to have more than 50 servers to manage in production. Doing tedious tasks manually like installing packages, patching newly found vulnerabilities such as the recent one found in the [Sudo command](https://blog.qualys.com/vulnerabilities-research/2021/01/26/cve-2021-3156-heap-based-buffer-overflow-in-sudo-baron-samedit)  on more than fifty servers will be time-consuming.

Provisioning technologies such as [Ansible](https://www.redhat.com/en/topics/automation/what-is-an-ansible-playbook#:~:text=An%20Ansible%C2%AE%20playbook%20is,make%20up%20an%20Ansible%20inventory) will help you automate the task. To automate all those tasks, you will need an identical production environment where you can test your Ansible playbooks to make sure they work as expected. Then, you move them to the production environment. Vagrant is good at spinning those virtual machine servers for you. You can use one of you favorite provisioning tool to configure your servers, test all behavior you need and destroy them. Isn't that good?

Since you understand where and when to use Vagrant, how to get started with Vagrant?

## Vagrant requirements
Vagrant only needs a hypervisor to function. In Vagrant documentation, they call them providers.

### Hypervisor? 
Taken from [Vmware](https://www.vmware.com/topics/glossary/content/hypervisor), one of the best virtualization software vendors
>"A hypervisor is software that creates and runs virtual machines (VMs). A hypervisor allows one host computer to support multiple guest virtual machines by virtually sharing its resources, such as memory and processing."

Vagrant supports many hypervisors(providers) such as **Virtualbox**, **Hyper-V**, and **Vmware** as well. Virtualbox is the easiest one to get by.

## Get Vagrant to work on your computer

First, you have to check if your processor support virtualization.
``` 
$grep --color vmx /proc/cpuinfo
```
If you get vmx flags in the output, Your processor support virtualization.

use `$grep --color svm /proc/cpuinfo` If your processor use AMD architecture.

### Installing hypervisor
Well, to install Virtualbox, you can use your default package manager, APT, or YUM because most Linux distribution provides VirtualBox in their package repositories. But be aware that most of the time, installing from the official packages repositories, you will get an old package version.
```
$sudo apt install virtualbox
```
**Note:** Be aware that I'm using Debian-based Linux distribution commands in this article (e.g: apt)

If you like to have the latest version, you can download it directly from [Virtualbox's official website](https://www.virtualbox.org/wiki/Linux_Downloads). You will have to deal with dependencies and updates by yourself.

### Installing vagrant
After getting the hypervisor to work, you will have to install Vagrant. Vagrant strongly advises downloading the binary from their [official website](https://www.vagrantup.com/downloads) instead of installing it using package managers.

Whether you choose to download the deb package, install it by running.
```
$sudo dpkg --install vagrant_version.number_x86_64.deb 
```
If you don't care about the latest version, install it using the package manager.
```
$sudo apt install vagrant
```
After installing Vagrant and the provider (VirtualBox), now you are good to go.

## Trying out Vagrant

Now that your environment is ready, how do you spin up your first virtual machine with vagrant? If you ever used VirtualBox or VMware Workstation before,  you know that to create a virtual machine, you have to download an ISO image that you will use to create the virtual machine.

With vagrant, where do you get Operating System(OS) images?
In the Vagrant world, what we take as OS images are called boxes, know that. These are package format vagrant use in its environment.
Vagrant has what we can take as a [marketplace](https://app.vagrantup.com/boxes/search) where you can find all boxes you need. Just try to browse the vagrant marketplace and find your favorite OS.

### How do I get started with one of my favorite boxes I want?
You can search the name of any Linux distribution box of your choice on https://app.vagrantup.com/boxes/search.

After getting the name of the box, you want to use, open your terminal and type `$vagrant init maintainer/box-name`.

That means that the box is **buster64** and the maintainer of the box is **debian**.
For example:
```
$vagrant init debian/buster64
```
*We will use this "debian/buster64" box for the rest of the tutorial.*

Now, look into your current directory, you find that vagrant has created a **Vagrantfile** for you in the current directory. This Vagrantfile includes all the configurations of the virtual machine you are about to start. Most of the configuration lines are comments. The most important part of this file is:
```
Vagrant.configure("2") do |config|
  config.vm.box = "debian/buster64"
end
```
That will tell vagrant that we want to use this box called ["debian/buster64"](https://app.vagrantup.com/debian/boxes/buster64), which is Debian 10.

Those are the minimum configuration lines we can have in Vagrantfile.

Next, start your virtual machine (VM).
```
$vagrant up
```
Vagrant will start to download the box we specified in the Vagrantfile from the vagrant cloud. After the download process, it is going to start the VM.
If nothing goes wrong, you now have a running virtual machine.

**Note:** Box downloading process happens only once. Next time, when you start another virtual machine with the same box name, it will use the downloaded one, the process will be fast.

Now, you can check the status of your virtual machine by typing.
```
$vagrant status
```
You will get a result telling you the current state of the virtual machine.
Current machine states and some other helpful details:
```
Current machine states:

default                   running (virtualbox)
```
Note that we have run all the previous commands in the directory where we created Vagrantfile.

To access the virtual machine, Just run.
```
$vagrant ssh
```
Now you are into the box, Congrat.
A new fresh environment to do whatever you want.

[congrat GIF]

After doing what you want and exiting the virtual machine, you can shut down the VM by using:
```
$vagrant halt
```
You can even use "suspend" to suspend the VM  (`$vagrant suspend`) so that you can come later and use 
(`$vagrant resume`) to resume the VM.

Nonetheless, you can destroy the virtual machine by using:
```
$vagrant destroy
```
It destroys all resources that were created during the machine creation process. After running this command, your computer should be left in a clean state, as if you never created the guest machine in the first place.

Most of the time, you will create Vagrantfile in different directories.
Use `$vagrant global-status` to get the status of all virtual machines you have on your local computer. That will help you not need to switch to different directories to get virtual machines status and finding out VMs you forget to shutdown.


## VM customization

Now we have created and destroyed a virtual machine. One way or another, we need to changes configurations on the virtual machines like hostname, memory, storage, networking (for IP addressing), and so on.

You saw that in the Vagrantfile we used to start the VM, it only has the mandatory `config.vm.box` config line that specifies the box we want to use.

Following that line that's where we can add other config lines such as:

`config.vm.hostname` to give the VM a hostname.

`config.vm.provision` to choose provisioners that can automate the installation of software and packages after VM is created.

`config.vm.provider` to give specific configuration according to a provider.
For example, you can dictate the provider (Virtualbox in this case) to create a VM with 2 vCPU and 1GB of memory.

```
Vagrant.configure("2") do |config|
  # ...
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 2
  end
end
```
`config.vm.network` to configure the IP address of the Virtual machine.

Well, on the networking part, there different ways you can choose to address the virtual machines.

Here are the three ones:

**1. Forwarded port:** By using this way, You forward one port on the host machine to the Virtual machine, I.e, When you access that port on the host machine, it will forward the request to the virtual machine.

`config.vm.network "forwarded_port", guest: 80, host: 9090`

You are forwarding port 9090 of the host machine to port 80 of the virtual machine. Anyone who can access port 9090 of this computer is forwarded to the virtual machine to handle his requests.

**2.Private network:** This setup allow the host only to access the virtual machine. That is a private network.

`config.vm.network "private_network", ip: "192.168.100.10"`

That will give the Virtual machine the IP address and the host computer will act as its gateway to other networks. 
You can create many virtual machines and give them the IP, which falls in the range of that private network, then they will be able to communicate privately in that virtual network you created. 
This is how I create a virtual lab with multiple machines.

**3.Public network:** This become a bridged network. That makes the virtual machine appear as another machine on your physical network.

`config.vm.network "public_network"`

Vagrant official documentation has more details about all you can change on the virtual machines https://www.vagrantup.com/docs

With all the previous explanations, Let's create a virtual machine quickly as we want. 
Below is an example of a Vagrantfile to create a virtual machine

```
Vagrant.configure("2") do |config|
  config.vm.box = "debian/buster64"
  config.vm.hostname = "test-vm"
  config.vm.network "private_network", ip: "192.168.100.10"
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 2
  end
end
```

This Vagrantfile states that we are using Debian 10 (buster) box, the virtual machine will have "test-vm" as a hostname with 1GB of memory, 2vCPU and be running on a private network. Only the host computer will have access to this virtual machine.

Run `$vagrant up`

![vagrant up](/assets/images/vagrantup.png "vagrant up")


Then, SSH into the box. `$vagrant ssh`

![vagrant ssh](/assets/images/vagrantsh.jpg "vagrant ssh")


Voila, access to the virtual machine in just seconds.
The memory is 1GB, and the hostname has changed also.

This article is just an introduction to what Vagrant can help you to do. There are tons of videos and books you can check yourself to get far with Vagrant, and virtualization in general.

If you are into building small virtual homelabs where you can hone your skills without spending a penny on renting servers in the cloud or buying home lab hardware, Vagrant is suitable for you.
I hope this was helpful to get you using Vagrant.


Credits:

- [Vagrant: Up and Running](https://www.oreilly.com/library/view/vagrant-up-and/9781449336103/), a book written by vagrant creator Mitchell Hashimoto.

- [Ansible for DevOps](https://www.ansiblefordevops.com/),a book by [Jeff Geerling](https://twitter.com/geerlingguy) teaching Ansible but using Vagrant based environment
