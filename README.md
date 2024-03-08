# chef-setup

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
```

Expected output
![image](https://github.com/tektutor/chef-setup/assets/12674043/40d69832-5e0a-40ee-a475-4cf3eb94fde8)
![image](https://github.com/tektutor/chef-setup/assets/12674043/3998a733-47db-4b31-8d43-f957ec7db831)
![image](https://github.com/tektutor/chef-setup/assets/12674043/9589c4a6-e4a1-4ffb-8ea5-ecbb012740f1)
![image](https://github.com/tektutor/chef-setup/assets/12674043/d5916bcd-e50a-44f1-9cd8-9009cbdcb614)
