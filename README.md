
# VM Cluster Building from Scratch
This guide will walk you through the process of building a High Performance Computing (HPC) cluster using the Beowulf architecture, entirely on virtual machines. By the end, you’ll have a functional VM-based cluster that can run distributed workloads—perfect for learning, experimentation, or low-cost parallel computing.

_Written by Thaksin Chiakon_

## Table of content

[1. Project set up](https://github.com/tkn13/hpc-guide/edit/main/README.md#%EF%B8%8F-project-setup)

[2. VM set up](https://github.com/tkn13/hpc-guide/edit/main/README.md#%EF%B8%8F-project-setup)


## 🛠️ Project Setup

We’ll use **CentOS Stream 10** as the base operating system for our cluster nodes. You can download the ISO from the official website: [CentOS Downloads](https://www.centos.org/download/).

To keep things organized—especially if you want to automate your setup or follow along more easily—here’s the suggested project directory structure:
```
vm-cluster/
├── disks/
└── network/
└── image/
    └── CentOS10.iso
```

## 💽 VM set up
**First things first**, we need multiple virtual machines to build our cluster. In this section, I’ll show you the proper way to create virtual machines suitable for a Beowulf-style HPC cluster.

You can use any virtualization software you're comfortable with—such as VirtualBox or VMware—but for this guide, we'll be using **QEMU** with **libvirt**, a powerful and flexible toolset for creating and managing virtual machines on Linux.

The idea is the same but it may use another method to get that . So i suggest that to use **QEMU** with **libvirt** to follow the guild easier.

#### 1. Virtual Disk setup
This command is for create own disk for your VM.
 ```bash
 qemu-img create -f <format> <disk_name> <size> 
 ```
##### Example
  ```bash
  cd /path_to_your_project/vm-cluster/disks
  qemu-img create -f qcow2 master_node_disk.qcow2 256G
  ```

##### Note

> When you ran the command it will create .qcow2 file where you current
> at so make sure you are in correct directory that you want to store
> your disk file.



No worry about your physical storage  <mark>qcow2</mark> is format for create virtaul disk, it's will not allocate your disk immediately instead it will allocate your disk as you use.

To check if disk was create successfully you can use this command
```bash
qemu-img info <filename> 
```
##### Example
  ```bash
qemu-img info master_node_disk.qcow2 
  ```
It  will showing something like
> image: master_node_disk.qcow2
> file format: qcow2
> virtual size: 256 GiB
> disk size: 5.04 GiB

The real disk usage is 5.04 GiB and the virtual size of the disk is 256GiB.

#### 2. Virtual Network setup
Since our cluster requires a network for internal communication, we will create a virtual private network using <mark>virsh</mark>.

  1. Create a Network XML File
  Example: <mark>private-net.xml</mark>
  ```xml
  <network>
    <name>hostonly-net</name>
    <bridge name='virbr10' stp='on' delay='0'/>
    <forward mode="nat">
    <ip address='192.168.100.1' netmask='255.255.255.0'>
      <dhcp>
        <range start='192.168.100.2' end='192.168.100.254'/>
      </dhcp>
    </ip>
  </network>
  ```
  2. Define network
  ```bash
  virsh net-define ./private-net.xml
  ```
  3. After define it you can check if the network have been define sucessfully 
  ```bash
  virsh net-list --all
  ```
  the output will show network name that define in xml file in this case it "hostonly-net"
  4. Start network
  ```bash
  virsh net-start hostonly-net
  virsh net-autostart hostonly-net
  ```
