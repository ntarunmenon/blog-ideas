Ansible is a control agent which means to use ansible it needs a target machine. This is a problem if you want to learn Ansible because where do you find a target machine from. 

The solution is to use vagrant which spins up virtual machine and doesent have the complexity of docker. These are the steps. I am using Ubuntu 18.04

### Install Virtualbox by using the following commands

```
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -

echo "deb [arch=amd64] http://download.virtualbox.org/virtualbox/debian bionic contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
```

### Install [Vagrant](https://www.vagrantup.com/intro/getting-started/index.html)

### Create a virtual ubuntu box by using the following command

```
vagrant init hashicorp/precise64
```
Uncomment the following line in generated Vagrantfile

config.vm.network :private_network, ip:"192.168.33.11"

```
vagrant up
```
This will create a box with ip 192.168.33.11

### You are done. Create an ansible inventroy file called hosts and add the ip 192.168.33.11 and run your first ansible command

```
 ansible all -i hosts --key-file .vagrant/machines/default/virtualbox/private_key -m ping -u vagrant
 ```

