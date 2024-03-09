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
sudo chef-server-ctl grant-server-admin-permissions jegan
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

cd /root/.chef
scp root@rhel-chef-server:/root/jegan.pem .

knife ssl fetch
knife ssl check
knife client list
```

Expected output
![image](https://github.com/tektutor/chef-setup/assets/12674043/3f45b02e-0719-488b-b718-c05575723b34)
![image](https://github.com/tektutor/chef-setup/assets/12674043/2ec9a93c-1e52-4b5a-b5fc-6435375f6d06)

## Bootstrapping rhel-chef-node 
```
```

Expected output
<pre>
[root@rhel-chef-workstation ~]# knife bootstrap rhel-chef-node --ssh-user root --ssh-password root --node-name rhel-chef-node
--ssh-user: This flag is deprecated. Use -U/--connection-user instead.
--ssh-password: This flag is deprecated. Use -P/--connection-password instead.
Connecting to rhel-chef-node using ssh
The authenticity of host 'rhel-chef-node (192.168.1.70)' can't be established.
fingerprint is SHA256:I9vKPyWhHx3Gv1QQJM2LKwn9TP9kwHACdRcoDEWN7uo.

Are you sure you want to continue connecting
? (Y/N) Y
Connecting to rhel-chef-node using ssh
Creating new client for rhel-chef-node
Creating new node for rhel-chef-node
Bootstrapping rhel-chef-node
 [rhel-chef-node] -----> Installing Chef Omnibus (stable/18)
 [rhel-chef-node] downloading https://omnitruck.chef.io/chef/install.sh
  to file /tmp/install.sh.2683/install.sh
 [rhel-chef-node] trying wget...
 [rhel-chef-node] el 8 x86_64
Getting information for chef stable 18 for el...
downloading https://omnitruck.chef.io/stable/chef/metadata?v=18&p=el&pv=8&m=x86_64
  to file /tmp/install.sh.2706/metadata.txt
 [rhel-chef-node] trying wget...
 [rhel-chef-node] sha1	6ce42182a2aca7011817f83eec5c7e812d3ebd0a
sha256	3ec242ae3bcb99be96e1f4a6619bc45217d19aae21b365c2af0e1319ea51e7f5
url	https://packages.chef.io/files/stable/chef/18.4.2/el/8/chef-18.4.2-1.el8.x86_64.rpm
version	18.4.2
 [rhel-chef-node] 
 [rhel-chef-node] downloaded metadata file looks valid...
 [rhel-chef-node] downloading https://packages.chef.io/files/stable/chef/18.4.2/el/8/chef-18.4.2-1.el8.x86_64.rpm
  to file /tmp/install.sh.2706/chef-18.4.2-1.el8.x86_64.rpm
 [rhel-chef-node] trying wget...
 [rhel-chef-node] Comparing checksum with sha256sum...
 [rhel-chef-node] Installing chef 18
 [rhel-chef-node] installing with rpm...
 [rhel-chef-node] warning: /tmp/install.sh.2706/chef-18.4.2-1.el8.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
 [rhel-chef-node] Verifying...                          
 [rhel-chef-node] ########################################
 [rhel-chef-node] Preparing...                          
 [rhel-chef-node] ########################################
 [rhel-chef-node] Updating / installing...
 [rhel-chef-node] chef-18.4.2-1.el8                     
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #

 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
^[ [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
^[ [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] #
 [rhel-chef-node] Thank you for installing Chef Infra Client! For help getting started visit https://learn.chef.io
 [rhel-chef-node] Starting the first Chef Infra Client Client run...
 [rhel-chef-node] +---------------------------------------------+
✔ 2 product licenses accepted.
+---------------------------------------------+
 [rhel-chef-node] Chef Infra Client, version 18.4.2
 [rhel-chef-node] Patents: https://www.chef.io/patents
 [rhel-chef-node] Infra Phase starting
 [rhel-chef-node] Resolving cookbooks for run list: []
 [rhel-chef-node] Synchronizing cookbooks:
 [rhel-chef-node] Installing cookbook gem dependencies:
Compiling cookbooks...
 [rhel-chef-node] Loading Chef InSpec profile files:
 [rhel-chef-node] Loading Chef InSpec input files:
Loading Chef InSpec waiver files:
 [rhel-chef-node] [2024-03-09T05:49:14+05:30] WARN: Node rhel-chef-node has an empty run list.
 [rhel-chef-node] Converging 0 resources
 [rhel-chef-node] 
Running handlers:
Running handlers complete
Infra Phase complete, 0/0 resources updated in 02 seconds
[root@rhel-chef-workstation ~]# 
[root@rhel-chef-workstation ~]# [root@rhel-chef-workstation ~]# knife bootstrap rhel-chef-node --ssh-user root --ssh-password root --node-name rhel-chef-node
bash: [root@rhel-chef-workstation: command not found...
[root@rhel-chef-workstation ~]# --ssh-user: This flag is deprecated. Use -U/--connection-user instead.
bash: --ssh-user:: command not found...
[root@rhel-chef-workstation ~]# --ssh-password: This flag is deprecated. Use -P/--connection-password instead.
bash: --ssh-password:: command not found...
[root@rhel-chef-workstation ~]# Connecting to rhel-chef-node using ssh
bash: Connecting: command not found...
[root@rhel-chef-workstation ~]# The authenticity of host 'rhel-chef-node (192.168.1.70)' can't be established.
> fingerprint is SHA256:I9vKPyWhHx3Gv1QQJM2LKwn9TP9kwHACdRcoDEWN7uo.
> 
> Are you sure you want to continue connecting
> ? (Y/N) Y
> Connecting to rhel-chef-node using ssh
> Creating new client for rhel-chef-node
> Creating new node for rhel-chef-node
> Bootstrapping rhel-chef-node
>  [rhel-chef-node] -----> Installing Chef Omnibus (stable/18)
>  [rhel-chef-node] downloading https://omnitruck.chef.io/chef/install.sh
>   to file /tmp/install.sh.2683/install.sh
>  [rhel-chef-node] trying wget...
>  [rhel-chef-node] el 8 x86_64
> Getting information for chef stable 18 for el...
> downloading https://omnitruck.chef.io/stable/chef/metadata?v=18&p=el&pv=8&m=x86_64
>   to file /tmp/install.sh.2706/metadata.txt
>  [rhel-chef-node] trying wget...
>  [rhel-chef-node] sha16ce42182a2aca7011817f83eec5c7e812d3ebd0a
> sha2563ec242ae3bcb99be96e1f4a6619bc45217d19aae21b365c2af0e1319ea51e7f5
> urlhttps://packages.chef.io/files/stable/chef/18.4.2/el/8/chef-18.4.2-1.el8.x86_64.rpm
> version18.4.2
>  [rhel-chef-node] 
>  [rhel-chef-node] downloaded metadata file looks valid...
>  [rhel-chef-node] downloading https://packages.chef.io/files/stable/chef/18.4.2/el/8/chef-18.4.2-1.el8.x86_64.rpm
>   to file /tmp/install.sh.2706/chef-18.4.2-1.el8.x86_64.rpm
>  [rhel-chef-node] trying wget...
>  [rhel-chef-node] Comparing checksum with sha256sum...
>  [rhel-chef-node] Installing chef 18
>  [rhel-chef-node] installing with rpm...
>  [rhel-chef-node] warning: /tmp/install.sh.2706/chef-18.4.2-1.el8.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
>  [rhel-chef-node] Verifying...                          
>  [rhel-chef-node] ########################################
>  [rhel-chef-node] Preparing...                          
>  [rhel-chef-node] ########################################
>  [rhel-chef-node] Updating / installing...
>  [rhel-chef-node] chef-18.4.2-1.el8                     
>  [rhel-chef-node] Thank you for installing Chef Infra Client! For help getting started visit https://learn.chef.io
>  [rhel-chef-node] Starting the first Chef Infra Client Client run...
>  [rhel-chef-node] +---------------------------------------------+
> ✔ 2 product licenses accepted.
> +---------------------------------------------+
>  [rhel-chef-node] Chef Infra Client, version 18.4.2
>  [rhel-chef-node] Patents: https://www.chef.io/patents
>  [rhel-chef-node] Infra Phase starting
>  [rhel-chef-node] Resolving cookbooks for run list: []
>  [rhel-chef-node] Synchronizing cookbooks:
>  [rhel-chef-node] Installing cookbook gem dependencies:
> Compiling cookbooks...
>  [rhel-chef-node] Loading Chef InSpec profile files:
>  [rhel-chef-node] Loading Chef InSpec input files:
> Loading Chef InSpec waiver files:
>  [rhel-chef-node] [2024-03-09T05:49:14+05:30] WARN: Node rhel-chef-node has an empty run list.
>  [rhel-chef-node] Converging 0 resources
>  [rhel-chef-node] 
> Running handlers:
> Running handlers complete
> Infra Phase complete, 0/0 resources updated in 02 seconds  
</pre>
