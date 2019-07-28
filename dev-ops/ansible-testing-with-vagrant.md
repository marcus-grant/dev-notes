Testing Ansible Roles & Playbooks Using Vagrant
===============================================


Overview
--------

Configuration
-------------

### Provider Do/End Block

The actual virtual machine configurations like memory, cpu, gui, headless, name, etc. occurs in a boiler plate ruby do/end loop like below where all settings in the iterated `vb` *which could be named anything as a variable* where each `vb` has its properties defined:

```ruby
config.vm.provider "virtualbox" do |vb|
    vb.someproperty = "somevalue"
end
```

### GUI vs. Headless

Default, VirtualBox start in headless mode, I create some workstation playbooks to make sure I have quick, consistent installs of my linux workstations, which of course have GUIs. This means I'll need to see that things like the Desktop environment, GUI apps like browsers or terminal emulators are installed and work as intended. Vagrant does support this by adding a line like this:

```ruby
config.vm.provider "virtualbox" do |vb|
    # ... all other configs
    vb.gui = true
end
```

### Virtual Machine Name

```ruby
config.vm.provider "virtualbox" do |vb|
    # ... all other configs
    vb.name = "some_vm_name"
end
```

### Linked Clones

New machines are usually created by importing the base box. Large boxes or having multiple ones can add up both in variety of boxes and and sheer storage volume required to persist them. Linked clones allows for differencing the base box image for each vm created and allows for only dealing with the variations of each to save on time (at least in terms of imports) and space. Just use the `.linked_clone` property of a vm:

```ruby
config.vm.provider "virtualbox" do |vb|
    vb.linked_clone = true
end
```

### Specific VBoxManage Customizations

Virtualbox has a command line and script utility known as [VBoxManage][02]. And with it you can get **very** specific about each VM's configuration before provisioning the machine. In this example it gets used to throttle the CPU to 50% freeing up the other half for the host.

```ruby
config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
end
```

The `customize` property is used to specify the VBoxManage calls vagrant will use. `:id` is a special parameter that gets inserted with the generated machine id virtual box will use that's required by the VBoxManage backend. Multiple `customize` properties can be specified to have many of these configurations strung up together.

If you read up on the [vagrant documentation][03] you'll find many convenience shortcuts like the ones defined before this section or other commonly used ones like `memory` or `cpu` below that provision a certain number of cpu cores or megabytes of RAM to the guest machine:

```ruby
config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024 # MiB
    vb.cpu = 1 # threads, hyperthreads or SMT threads are counted as a cpu
end
```



Setting Up an Initial Playbook
------------------------------

Start by creating a playbook file:

```sh
touch playbook.yml
```

Add this to `playbook.yml`:

```sh
---
- name: Web01
  hosts: all
  become: yes
  gather_facts: False
  pre_tasks:
    - name: Install python for Ansible
      raw: test -e /usr/bin/python || (apt -y update && apt install -y python-minimal)
      changed_when: False
    - setup: # gather_facts
```

References
----------

1. [Medium - Per Wagner Nielsen: Ansible Tutorial Part 1: Inventory Files, Vagrant & Remote Hosts][01]
2. [VirtualBox Docs: VBoxManage][02]
3. [Vagrant Docs: Main Page][03]
4. [Manjaro Wiki: VirtualBox][04]

[01]: https://medium.com/@perwagnernielsen/ansible-tutorial-part-1-inventory-files-vagrant-and-remote-hosts-33a15b0185c0 "Medium - Per Wagner Nielsen: Ansible Tutorial Part 1: Inventory Files, Vagrant & Remote Hosts"
[02]: https://www.virtualbox.org/manual/ch08.html "VirtualBox Docs: VBoxManage"
[03]: https://www.vagrantup.com/docs/index.html "Vagrant Docs: Main Page"
[04]: https://wiki.manjaro.org/index.php?title=Virtualbox "Manjaro Wiki: VirtualBox"
