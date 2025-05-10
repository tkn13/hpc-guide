# HPC Build Guide
This guild will show how to build HPC using Beowulf cluster architecture.
write by Thaksin Chiakon

## VM set up
This section will how to build virtual machine for simulate building cluster if you have real computers, skip this section

### KVM
####1. Virtual Disk setup
This command is for create own disk for your VM.
 ```bash
 qemu-img create -f <format> <disk_name> <size> 
 ```
#####Example
  ```bash
  mkdir disk
  cd disk
  qemu-img create -f qcow2 master_node_disk.qcow2 60G
  ```
 <mark>qcow2</mark>  is a storage format for virtual disks

####2. Virtual Network setup
Since our cluster requires a network for internal communication, we will create a virtual private network using <mark>virsh</mark>.

  1. Create a Network XML File
  Example: <mark>private-net.xml</mark>
  ```xml
  <network>
    <name>hostonly-net</name>
    <bridge name='virbr10' stp='on' delay='0'/>
    <ip address='192.168.100.1' netmask='255.255.255.0'>
      <dhcp>
        <range start='192.168.100.2' end='192.168.100.254'/>
      </dhcp>
    </ip>
  </network>
  ```
