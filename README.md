## kvm-install-vm

A bash wrapper around virt-install to build virtual machines on a local KVM
hypervisor.  Tested on CentOS 7.

### Usage

```
OPTIONS
  -c          Number of vCPUs     (default: 1)
  -m          Memory Size (MB)    (default: 1024)
  -d          Disk Size (GB)      (default: 10)
  -t          Linux Distribution  (default: centos7)
  -l          Location of Images  (default: /var/lib/libvert/images)
  -k          SSH Public Key      (default: /root/.ssh/id_rsa.pub)
  -b          Bridge              (default: br0)
  -h          Display help
  -i          Custom QCOW2 Image
  -n vmname   Name of VM to create
  -r vmname   Name of VM to delete

DISTRIBUTIONS
 - centos7
 - centos6

EXAMPLES

Create VM with default params:
  ./kvm-install-vm -n foo

Create VM with custom params:
  ./kvm-install-vm -c 2 -m 2048 -d 20 -n foo

Remove (destroy and undefine) a VM:
  ./kvm-install-vm -r foo
```

### Notes

- This will download a cloud image from the CentOS site if the default QCOW2
  image doesn't exist.

### Expanding Virtual Machine Disks
1. Shut down the VM 
2. Resize the file system
3. Verify the file system has expanded
4. Boot the VM again

```bash
virsh shutdown guest
cd /var/lib/libvirt/images/guest
qemu-img resize guest.qcow2 +20G
cp guest.qcow2 guest-orig.qcow2
virt-resize --expand /dev/sda1 guest-orig.qcow2 guest.qcow2
virt-filesystems --long -h --all -a guest.qcow2
virsh start guest
```

Next, log into the VM and run the following to expand the partition
```bash
sudo xfs_growfs /
```

If everything works then the `guest-orig.qcow2` file can be deleted.
