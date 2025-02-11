# Linux-Template-for-Proxmox
Creating a Linux template in Proxmox involves setting up a virtual machine (VM) with a Linux distribution, configuring it, and then converting it into a template. Below is a step-by-step guide to creating a Linux template in Proxmox:

1) Start the VM Creation Process:


Go to Datacenter -> Node (select the node where you want to create the VM).
Click on Create VM .

**Configure General Settings:**
VM ID: Enter a VM ID 
Name: Provide a name for your VM (e.g., Debian-12-Template).
![image](https://github.com/user-attachments/assets/8d50662e-0c04-45d7-b5eb-f071fec48e7e)
OS Settings:

ISO Image: Select Do not use any media (since you're using a cloud image, not an ISO).

![image](https://github.com/user-attachments/assets/a164073e-6e69-4e85-816b-80cac6b4bd7c)

**System Settings:**

 Keep the default 
Qemu Agent: Ensure that this option is selected.
![image](https://github.com/user-attachments/assets/aa0580b3-e306-4c60-ad94-b0c84d420132)

**Disk Settings:**

Delete Default Disk: Go ahead and delete any default disks created by Proxmox

![image](https://github.com/user-attachments/assets/3a432584-58a8-4ce8-8198-179f135c6da9)

**CPU, Memory, and Network Settings:**

CPU: Set the number of cores as per your needs (typically, 2 cores are sufficient for a template).
Memory: Allocate a reasonable amount of RAM (e.g., 2048 MB or 4096 MB depending on your needs).
Network: Configure the network settings as needed. The default network settings should be adequate for most setups.
**
Confirm and Create:**

Review your settings.
Click Finish to create the VM.
![image](https://github.com/user-attachments/assets/1f2ad432-c93a-4cdf-bc8e-ba89e6230125)

2 ) Download the Debian 12 Cloud Image from https://cloud.debian.org/images/cloud/  to master node ( VM )

Use wget to download the genericcloud-amd64 .qcow2 image from the Debian Cloud Images on your Proxmox master node.

wget https://cloud.debian.org/images/cloud/2024-07-17/debian-12-genericcloud-amd64-20240717-1811.qcow2

![image](https://github.com/user-attachments/assets/43ae6bd5-c094-4202-bd9d-ea96f4f4079c)

Import the downloaded image into Proxmox using the qm importdisk command. Replace 5000 with template VM ID and local-lvm with the storage name.

![image](https://github.com/user-attachments/assets/b7a9c93f-05b3-4202-b953-c28c8b2acaad)

change some settings in the template VM 

In VM hardware section edit Unused Disk 0 and set SCSI 0

change some settings in the template VM 

In VM hardware section edit Unused Disk 0 and set SCSI 0

Go to the Options section of the VM, and click Edit on the Boot Order setting.
Set SCSI 0 as the primary boot device

![image](https://github.com/user-attachments/assets/6265a283-cbbd-4a96-a953-ce9c557dc665)

Set Start at Boot to Yes to ensure the VM starts automatically if the Proxmox server restarts.

Add Cloud-Init Drive:

Go to the Hardware section.
Click Add and select Cloud-Init Drive.
Set the Bus/Device to IDE 0 and select local storage.

![image](https://github.com/user-attachments/assets/93515275-398a-4a09-9a72-5a8554bd709c)

Navigate to the Cloud-Init section of the VM.
Set the desired Password for the cloud-init configuration

![image](https://github.com/user-attachments/assets/b9105420-46f4-4af1-8ef5-06bf9b084a95)

now start the VM and run below command in root access to remove unnecessary contents

truncate -s0 /etc/hostname
truncate -s0 /etc/machine-id
rm /var/lib/dbus/machine-id
ln -s /etc/machine-id /var/lib/dbus/machine-id
rm /etc/ssh/ssh_host_*
history -c
rm ~/.bash_history
apt clean
rm -rf /tmp/*
rm -rf /var/tmp/*
find /var/log -type f -exec truncate -s0 {} \;
cloud-init clean --logs
rm -f /var/lib/systemd/random-seed
rm -f /etc/udev/rules.d/70-persistent-net.rules
rm -rf /home
poweroff

now all the necessary configurations are completed.

Convert your VM into a template (right-click on VM and click on Convert to template )

![image](https://github.com/user-attachments/assets/f229fd8a-d063-46c3-9c96-88477ee336bb)

To create a new VM from the template, clone, assign a unique VM ID, name, and select Full Clone mode.
![image](https://github.com/user-attachments/assets/26ea0ca1-306e-4e72-8691-0ee60800b61a)


