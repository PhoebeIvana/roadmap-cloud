## 📥 How to Install Cloudstack 

**A. Modifying the Network Interface Configuration**
1. Modify the NIC in netplan with the shown configuration.
```
cd /etc/netplan
nano 01-netplan.yaml
```

```
network:
   version: 2
   renderer: networkd
   ethernets:
     eno1:
       dhcp4: false
       dhcp6: false
       optional: true
     eno2:
       dhcp4: false
       dhcp6: false
       optional: true
   bridges:
     cloudbr0:
       addresses: [192.168.10.33/24]
       routes:
        - to: default
          via: 192.168.10.1
       nameservers:
         addresses: [1.1.1.1,8.8.8.8]
       interfaces: [eno1]
       dhcp4: false
       dhcp6: false
       parameters:
         stp: false
         forward-delay: 0
```
change the `addresses` to the IP address of your service. Then, channge the `via` to your default gateway. 

2. Apply the changes in netplan configuration, using the following command:

```
sudo -i
netplan generate
netplan apply
reboot
```

**B. Update the System, and Install Any Required Tools**
1. Use the following command

```
sudo apt update
sudo apt install htop
sudo apt install lynx
sudo apt install duf
sudo apt install htop lynx duf -y
sudo apt install bridge-utils
```

**C. Configure LVM in the Ubuntu**
1. Do it with the following command
```
sudo vgextend ubuntu-vg /dev/sda
sudo vgextend ubuntu-vg /dev/sdb
sudo lvextend -L +100G /dev/ubuntu-vg/ubuntu-lv
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

**D. Install SSH Server and Other Tools**
1. Do it using the following command
```
sudo -i # get root shell
apt-get install openntpd openssh-server vim htop tar
apt-get install intel-microcode
passwd root
```

**E. Enabling the Root Login**
1. The purpose is to open the SSH server configuration file, by using the nano text editor. What we'll do is to uncommenting the `PermitRootLogin yes` line, and restart the SSH service in order to apply changes. 
```
nano /etc/ssh/sshd_config
#PermitRootLogin yes
#restart ssh service
service ssh restart
```

**F. Cloudstack Management Server Setup**
1. Let setup up the environment for installing and managing the CloudStack infrastructure. What we'll do is creating the necessary directory, retrieves and configures the GPG key for the CloudStack repo, and also adding the repository source. 
```
mkdir -p /etc/apt/keyrings
wget -O- http://packages.shapeblue.com/release.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/cloudstack.gpg > /dev/null

echo deb [signed-by=/etc/apt/keyrings/cloudstack.gpg] http://packages.shapeblue.com/cloudstack/upstream/debian/4.17 / > /etc/apt/sources.list.d/cloudstack.list
```






 
