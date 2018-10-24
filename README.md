# debutsav November 24-25 @ CUSAT, Ernakulam
  Using Debian Cloud images for Debutsav workshops



## Video Links

Hypervisor  | Links
--------- | ---------
KVM on any Linux distribution | [youtube video link](https://youtu.be/yeZk7_c0DeI)


## Prerequisites
- Laptop should be capable enough to run atleast one Debian VM instance.
    - 1 GB of RAM should be allocated for the VM
    - 1 vCPU should be allocated for the VM
    - Atleast 5GB of free space should be available on the partition where the VM image is downloaded or resides. 
- Laptop should have Hypervisors installed Like VirtualBox,HyperV, KVM or Vmware Workstation
- Download the Debian cloud images provided by the Debian Cloud Team. Refer the beow tables for the required Links

## Image downloads

Image format| OS  | hypervisor | Image download Link
------------| ----|------------|-------
Qcow2| Linux | KVM| https://cloud.debian.org/images/cloud/OpenStack/9.5.6-20181013/debian-9.5.6-20181013-openstack-amd64.qcow2

## Steps for Linux distribution configured with KVM

- Create Cloud-init ISO file

Create a directory and two files should be created with the following contents. For example, path shown here is `/vm`. Replace the path as per your system configuration

        mkdir /vm/debustav
        cd /vm/debutsav

The first file should be named as `user-data`. Include the following contents. Note password for the debian user is set as `passw0rd`. Change the password as required.

~~~
#cloud-config
password: passw0rd
chpasswd: { expire: False }
ssh_pwauth: True
~~~

Create another file called `meta-data`. Include the following contents. Hostname of the VM is set as `debian-node-0`

~~~
instance-id: debiannode0
local-hostname: debian-node-0
~~~
- Create an ISO image using the two files. Execute the following command. This will create an iso image called `debiancloudinit.iso`. `genisoimage` package should be installed on the system.
~~~
genisoimage -output debiancloudinit.iso -volid cidata -joliet -rock user-data meta-data
~~~
- Create the VM instance using command line tool. Package `virt-install` should be installed on the node. For GUI steps, refer the youtube video. In the below command, modify the path mentioned after `--disk`. You have to modify two paths.
~~~
virt-install --import --name debianvm --memory 1024 --vcpus 1 --disk /vm/new-debian-9.5.6-20181013-openstack-amd64.qcow2,format=qcow2,bus=virtio --disk /vm/debutsav/debiancloudinit.iso,device=cdrom --network bridge=virbr0,model=virtio  --noautoconsole
~~~
- Execute `virt-manager` to access the VM or `virsh console debianvm`. In the virsh command, `debianvm` was the name of the VM that was created using the `virt-install` command. To exit from `virsh command`, use `CTRL+]`
- To login to the virtual machine, use the following credentials
~~~
username: admin
password: passw0rd
~~~

Note incase if you have mentioned a different password in user-data file, then use the same one. `admin` user has sudo access.

