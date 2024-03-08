# Chef setup

We would need atleast 3 virtual machines with RHEL 8.9 64-bit OS
- Quad Core Processor
- Atleast 8GB RAM
- Atleast 20GB HDD/SSD

I would recommend to name the hostname as shown below to easily identify which VM hosts Chef Server, Workstation and Nodes.

Virtual Machine - 1 
- hostname ( rhel-chef-server )

Virtual Machine - 2
- hostname ( rhel-chef-workstation )

Virtual Machine - 3
- hostname ( rhel-chef-node )

## Installing Chef Server in RHEL 8.9
```
wget https://packages.chef.io/files/stable/chef-server/15.9.20/el/8/chef-server-core-15.9.20-1.el8.x86_64.rpm
rpm -ivh ./chef-server-core-15.9.20-1.el8.x86_64.rpm
chef-server-ctl reconfigure
chef-server-ctl reconfigure

chef-server-ctl status
```

Expected output
![image](https://github.com/tektutor/chef-setup/assets/12674043/40d69832-5e0a-40ee-a475-4cf3eb94fde8)
![image](https://github.com/tektutor/chef-setup/assets/12674043/3998a733-47db-4b31-8d43-f957ec7db831)
![image](https://github.com/tektutor/chef-setup/assets/12674043/9589c4a6-e4a1-4ffb-8ea5-ecbb012740f1)
![image](https://github.com/tektutor/chef-setup/assets/12674043/d5916bcd-e50a-44f1-9cd8-9009cbdcb614)
![image](https://github.com/tektutor/chef-setup/assets/12674043/b27957d2-7453-4335-8462-87b73d799d09)

We need to create an Administrative user
```
sudo chef-server-ctl user-create jegan Jeganathan Swaminathan jegan@tektutor.org admin@123 --filename jegan.pem
sudo chef-server-ctl org-create tektutor 'TekTutor' --association_user jegan --filename tektutor-validator.pem
sudo chef-server-ctl org-show
```

Expected output
![image](https://github.com/tektutor/chef-setup/assets/12674043/01d7a3df-bd40-4760-9b4a-f04e610cbd0e)


Configure the firewall to open up 443(https) port for Chef Workstation and Chef nodes to connect to the Chef Server
```
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
firewall-cmd --list-all
```

## Installing Chef Workstation
```
wget https://packages.chef.io/files/stable/chef-workstation/24.2.1058/el/8/chef-workstation-24.2.1058-1.el8.x86_64.rpm
rpm -ivh ./chef-workstation-24.2.1058-1.el8.x86_64.rpm
chef -v
```

Expected output
![image](https://github.com/tektutor/chef-setup/assets/12674043/9bcf6541-db4f-40d0-8723-2b209aa1850b)
![image](https://github.com/tektutor/chef-setup/assets/12674043/cc6430e5-bdb5-402e-ab8b-7e560f74b729)

We need to add the ruby that comes with the Chef workstation
```
echo $SHELL
echo 'eval "$(chef shell-init bash)"' >> ~/.bashrc
source ~/.bashrc
which ruby
ruby --version
```

Integrating Chef Workstation with Chef Infra Server
```
knife configure

```

Expected output
![image](https://github.com/tektutor/chef-setup/assets/12674043/3f45b02e-0719-488b-b718-c05575723b34)
