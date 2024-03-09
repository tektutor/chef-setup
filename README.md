# Chef setup

I used 6 Virtual machines with the below configuration
- Quad Core Processor
- Atleast 8GB RAM
- Atleast 20GB HDD/SSD

![image](https://github.com/tektutor/chef-setup/assets/12674043/ea96b8fc-5f2d-4d4c-85ec-ce5b40bd0faa)

I would recommend to name the hostname as shown below to easily identify which VM hosts Chef Server, Workstation and Nodes.

Virtual Machine - 1 
- hostname ( rhel-chef-server )
- /etc/hosts
![image](https://github.com/tektutor/chef-setup/assets/12674043/901b3575-e096-40be-8a06-a6a412433fdb)
 
Virtual Machine - 2
- hostname ( rhel-chef-workstation )
- /etc/hosts
![image](https://github.com/tektutor/chef-setup/assets/12674043/36d76d80-a8f7-44eb-ba79-168a65bd8bfa)

Virtual Machine - 3
- hostname ( rhel-chef-node )
- /etc/hosts
![image](https://github.com/tektutor/chef-setup/assets/12674043/cf8eb2c0-9401-45f9-b8d9-da07d22a71b4)

Virtual Machine - 4
- hostname ( rhel-chef-automate )
- /etc/hosts
![image](https://github.com/tektutor/chef-setup/assets/12674043/63ccae12-e71f-4aa4-ad4d-f71dd6731b09)

Virtual Machine - 5
- hostname ( ubuntu-chef-node )
- /etc/hosts
![image](https://github.com/tektutor/chef-setup/assets/12674043/b841925f-2fb0-48c0-bd04-eea3c41c4d95)

Virtual Machine - 6
- hostname ( windows-chef-node )
- C:/windows/system32/drivers/etc/hosts
![image](https://github.com/tektutor/chef-setup/assets/12674043/2742b9f6-bf92-4a4c-a4e2-b59a57018bbd)

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

At this point, we should see the rhel-chef-node connected with the server
```
knife node list
```

Expected output
<pre>
[root@rhel-chef-workstation ~]# knife node list
rhel-chef-node 
</pre>


## Bootstrapping ubuntu-chef-node
```
knife bootstrap ubuntu-chef-node --ssh-user root --ssh-password root --node-name ubuntu-chef-node
```

Expected output
<pre>
[root@rhel-chef-workstation ~]# knife bootstrap ubuntu-chef-node --ssh-user root --ssh-password root --node-name ubuntu-chef-node
--ssh-user: This flag is deprecated. Use -U/--connection-user instead.
--ssh-password: This flag is deprecated. Use -P/--connection-password instead.
Connecting to ubuntu-chef-node using ssh
The authenticity of host 'ubuntu-chef-node (192.168.1.64)' can't be established.
fingerprint is SHA256:tgBrKcHWucpZGcGdT7y4BxyV5aV25YCGbuDvDzjn41Q.

Are you sure you want to continue connecting
? (Y/N) Y
Connecting to ubuntu-chef-node using ssh
Creating new client for ubuntu-chef-node
Creating new node for ubuntu-chef-node
Bootstrapping ubuntu-chef-node
 [ubuntu-chef-node] -----> Existing Chef Infra Client installation detected
 [ubuntu-chef-node] Starting the first Chef Infra Client Client run...
 [ubuntu-chef-node] Chef Infra Client, version 18.4.2
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Patents: https://www.chef.io/patents
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Infra Phase starting
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Resolving cookbooks for run list: []
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Synchronizing cookbooks:
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Installing cookbook gem dependencies:
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Compiling cookbooks...
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Loading Chef InSpec profile files:
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Loading Chef InSpec input files:
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Loading Chef InSpec waiver files:
 [ubuntu-chef-node] 
 [ubuntu-chef-node] [2024-03-09T05:55:39+05:30] WARN: Node ubuntu-chef-node has an empty run list.
 [ubuntu-chef-node] Converging 0 resources
 [ubuntu-chef-node] 
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Running handlers:
 [ubuntu-chef-node] 
 [ubuntu-chef-node] Running handlers complete
 [ubuntu-chef-node] Infra Phase complete, 0/0 resources updated in 02 seconds
 [ubuntu-chef-node]  
</pre>

## Node list ( From rhel-chef-workstation machine )
Once all the chef nodes are bootstrapped it would look as shown below
```
knife node list
```
Expected output
<pre>
[root@rhel-chef-workstation ~]# knife node list
rhel-chef-node
ubuntu-chef-node
windows-chef-node 
</pre>

## Enabling Chef Server Management Console ( Do this on the Chef Server machine )
```
chef-server-ctl install chef-manage

chef-server-ctl reconfigure

chef-manage-ctl reconfigure
```

Accessing the Chef Server Management console

![image](https://github.com/tektutor/chef-setup/assets/12674043/1907af53-843b-4784-8715-67531dcba3a0)
![image](https://github.com/tektutor/chef-setup/assets/12674043/5b24537f-e6be-4e09-99cf-3643e54b5fc2)
![image](https://github.com/tektutor/chef-setup/assets/12674043/9b75076a-1c92-44e6-8d7c-5a42f7d6db9f)

Login Credentials ( The user we created with chef-server-ctl and associated with tektutor organization )
<pre>
username - jegan
password - admin@123
</pre>
![image](https://github.com/tektutor/chef-setup/assets/12674043/028e4cc1-0171-4567-97e9-d95a4df62c5a)

## Installing Chef Automate in RHEL v8.9
```
curl https://packages.chef.io/files/current/latest/chef-automate-cli/chef-automate_linux_amd64.zip | gunzip - > chef-automate && chmod +x chef-automate
sudo ./chef-automate init-config

```

Expected output
<pre>
root@tektutor.org:~# ssh rhel-chef-automate
root@rhel-chef-automate's password: 
Activate the web console with: systemctl enable --now cockpit.socket

Register this system with Red Hat Insights: insights-client --register
Create an account or view all your systems at https://red.ht/insights-dashboard
Last login: Sat Mar  9 06:35:26 2024 from 192.168.1.104
[root@rhel-chef-automate ~]# pwd
/root
[root@rhel-chef-automate ~]# curl https://packages.chef.io/files/current/latest/chef-automate-cli/chef-automate_linux_amd64.zip | gunzip - > chef-automate && chmod +x chef-automate
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 13.2M  100 13.2M    0     0  2595k      0  0:00:05  0:00:05 --:--:-- 3394k
[root@rhel-chef-automate ~]# ls
anaconda-ks.cfg  chef-automate  initial-setup-ks.cfg
[root@rhel-chef-automate ~]# ./chef-automate init-config
Success: Config written to config.toml
Automate Load Balancer fqdn set to [rhel-chef-automate]
When Automate is deployed you will access https://rhel-chef-automate to see the dashboard.
If this is not a routable address please update the fqdn appropriately before deploying.
[root@rhel-chef-automate ~]# cat config.toml 

# This is a default Chef Automate configuration file. You can run
# 'chef-automate deploy' with this config file and it should
# successfully create a new Chef Automate instance with default settings.

[global.v1]
  # The external fully qualified domain name.
  # When the application is deployed you should be able to access 'https://<fqdn>/'
  # to login.
  fqdn = "rhel-chef-automate"

  # TLS Certificates for External-Facing Services
    # The following TLS certificate and RSA public key were
    # automatically generated. The certificate is a self-signed
    # certificate and will likely produce security warnings when you
    # visit Chef Automate in your web browser. We recommend using a
    # certificate signed by a certificate authority you trust.
  [[global.v1.frontend_tls]]
    # The TLS certificate for the load balancer frontend.
    cert = """-----BEGIN CERTIFICATE-----
MIIDvzCCAqegAwIBAgIQOuqjSNz2llcXKyrJEBEZnzANBgkqhkiG9w0BAQsFADBa
MQswCQYDVQQGEwJVUzEWMBQGA1UEChMNQ2hlZiBTb2Z0d2FyZTEWMBQGA1UECxMN
Q2hlZiBBdXRvbWF0ZTEbMBkGA1UEAxMScmhlbC1jaGVmLWF1dG9tYXRlMB4XDTI0
MDMwOTAxNDEzMloXDTM0MDMwNzAxNDEzMlowWjELMAkGA1UEBhMCVVMxFjAUBgNV
BAoTDUNoZWYgU29mdHdhcmUxFjAUBgNVBAsTDUNoZWYgQXV0b21hdGUxGzAZBgNV
BAMTEnJoZWwtY2hlZi1hdXRvbWF0ZTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
AQoCggEBAMJMcmhaxJojvJjBMPsZ/11yPSO8ZYsZ0BlL3oR4eB7iO1aMZspSzd12
MXcS+J7hLcGIy2WbwONpDdHbKzwpwb5uoOA8Bo0GEGtt8Tn9EcmIKphgF2y/zsTo
XB8EwMbI4AiJ2tkQgXfdo/BoFMF8LqlJz3AWj5dt13XXx9xV19c0H4cPE1S+W746
Ejx9td3ImKN8JH3wx635NwbIMuMSIcZ3Mxjkw+57KbGcr+wAHUiMwc4VhABXrR7z
s6HG++/vRLO8WDVDXa3B0zh+0Vzb5mhfZ9VJUKSgpji4vTgvJX5QV8zsjugP+GAH
XRa51RzH9pdpGr3Xk/jcBueYVdGrI2kCAwEAAaOBgDB+MA4GA1UdDwEB/wQEAwIC
pDAdBgNVHSUEFjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwDwYDVR0TAQH/BAUwAwEB
/zAdBgNVHQ4EFgQUb7zney1p3rHTpzKIxS1YGuNbZIMwHQYDVR0RBBYwFIIScmhl
bC1jaGVmLWF1dG9tYXRlMA0GCSqGSIb3DQEBCwUAA4IBAQBcr+zAWH4orJ5KPj/N
+2Wtm+b6tczh8i4iVLv+okEdD5LjhFc7bOecl6aoQ4SC2dXQYGv2I4XM4YaJGErP
ciY3m4vvrgBtlSfquqqgMO51C35UI83BvVAkKW2xFvb1H/OFcLUvb/XbKPC+aSn2
D9zFTCP5gDfbpPKdFK2E3IgwvRpQ3okalszaERLUIk9f6nPYju/3WBwZqNQZv74b
CwOjdXmsPdv0zYd35H2ws4BQ+7VK+jsaTbT1c740mVkAwJQBwHo7P/5b+jAqV9qx
MIFXqwRaMrYapmN/lxhLlB4YgPR6hX7VeVh19zwgyDwOzGFJ4yiMfQjlJAG5Ct5s
o4M6
-----END CERTIFICATE-----
"""

    # The TLS RSA key for the load balancer frontend.
    key = """-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAwkxyaFrEmiO8mMEw+xn/XXI9I7xlixnQGUvehHh4HuI7Voxm
ylLN3XYxdxL4nuEtwYjLZZvA42kN0dsrPCnBvm6g4DwGjQYQa23xOf0RyYgqmGAX
bL/OxOhcHwTAxsjgCIna2RCBd92j8GgUwXwuqUnPcBaPl23XddfH3FXX1zQfhw8T
VL5bvjoSPH213ciYo3wkffDHrfk3Bsgy4xIhxnczGOTD7nspsZyv7AAdSIzBzhWE
AFetHvOzocb77+9Es7xYNUNdrcHTOH7RXNvmaF9n1UlQpKCmOLi9OC8lflBXzOyO
6A/4YAddFrnVHMf2l2kavdeT+NwG55hV0asjaQIDAQABAoIBAADgp4OhJVIs5yUp
ejcoHuclJPGMYEkIjNAQb6jZgnFhRDNX3v768MMXTZHkHAtbqQE+xOxoj653QwC3
pLJY3e8mQMPCgaURafGSRYylf/cRDmAxzulG1IA/LGtXcidzsM9ZEAv67xrDfYIQ
dkeJcAyDsNQoxe1BKb/2gYiVOqxWJel/br0qUDad98mkHC7ZQ5XgEiqcoPWS9NWY
cXKPUQhYdpnkFZpHuIZU0d8mSPQHlSZ/niTzIekaNNrzwF9CJbYInWFlTBBoeCsw
f1A4EAWC19m3dOyY4YuloiAWeDMx9JuOjZNYxT6Obcmitd+y65gL2KNduBVAw6RM
xK+r0AECgYEA0SvVBmil8CaEtZFhD4ynklBDSPJlpJS//WmULDz/ZK7WPWSGXP2m
FIqcaddvOgPx4tisu0Z/YBWImcDyXn0VUc3qLhR2QvnLpWl7OejuXYlbaA0n5nXM
vbNSq9wPV6LiWqmoFvhQTX6ggqAqrIfTdJcbwd3DV6wS2FO1lNpgDUECgYEA7cw5
2INmy6cehoUlc1NWRVb6jR3h/plL5hwZLZCn2Q9qhPc8Ptd4tfjdgOrqvqvs4J5C
LI86JseWYYZcHk4T69KDGo0wKoaG+jY4x0QJAJdZTt18iUV7kZPyX3Bd01YbIeJD
fTZeSpkJJoP3Xdl1HpIVZ2T1k6zrAK807Y8UBCkCgYAMBrEgxTOFMzG+HrWt5zBb
QTIdJ5Kge8zuDPvv7zHf4A2BSh5PimXaGH4Vt8U1WLS3ji2gNtVr+ND5xhYpSJfJ
NEWakKKcBzCmOgJTugi6aANRYDBDF9LFtWGzb9uR9lN9UvPPHke/rOAdoofRk52c
vnNiGhX5HHOY7HcsL8IGAQKBgEm2Q7wcx1GbpTe3sCs2iwiFdLcnqzU7ma5jyV45
d/4t7Cv5Y4BrVGy8DxV2PHwMWSorhIHBiBobRj9G4ek29K/mGmOxnlugRdjr09/Y
9B1tzO5Ic+EtW6d9v+TbtQncqB5xv8/heYsQMyjwQU6jKM3oJKBfSEyVjmcw6Gw3
LNI5AoGAA1UNDx0drB6yONu1Bc6tjzouVHzJZJ5dvzUjMGzxj3zM3/nnQdRunHl4
5cxLmasfceAhfNiZL6lLSokB4QvGo22dLfG2nuS0aNGywUNe0ieYtJQWh5C+TGNZ
HpEDa0+o98reEYEVO3Cxx+OxtMmD/KZf23RzjPqZFKBYyekp/XQ=
-----END RSA PRIVATE KEY-----
"""


# Deployment service configuration.
[deployment.v1]
  [deployment.v1.svc]
    # Habitat channel to install hartifact from.
    # Can be 'dev', 'current', or 'acceptance'
    channel = "current"
    upgrade_strategy = "at-once"
    deployment_type = "local"

# License Control service configuration.
[license_control.v1]
  [license_control.v1.svc]
    # The Chef Software provided license token required to run Chef Automate.
    # This can also be set with the "chef-automate license apply" command.
    license = ""

# Chef Automate services can be configured to better meet the needs of
# your particular installation. For details see:
#
# https://automate.chef.io/docs/configuration/
#

[opensearch.v1.sys.runtime]
  heapsize = "4g"

[root@rhel-chef-automate ~]#  
</pre>

We need to updated the config.toml before we deploy the Chef automate as we are using a separate stand-alone Chef Infra Server on a different machine.
```
[erchef.v1.sys.data_collector]
  enabled = false
```
![image](https://github.com/tektutor/chef-setup/assets/12674043/32b7bccb-4bde-4f20-989f-c028577e7705)


Let's deploy the chef automate server with the updated config.toml file
```
sudo chef-automate deploy --product infra-server config.toml
```

Expected output
![image](https://github.com/tektutor/chef-setup/assets/12674043/b1dc3897-1fe7-4a70-9ce6-40193871971c)
![image](https://github.com/tektutor/chef-setup/assets/12674043/8d5b734e-2614-41d4-aa3b-b59030f22748)
![image](https://github.com/tektutor/chef-setup/assets/12674043/24a1fdb2-ae12-4834-90f0-401b95f6c9a3)

Let's configure selinux to permissive
![image](https://github.com/tektutor/chef-setup/assets/12674043/e6843189-279c-43c0-94d5-d83237710b70)
```
setenforce 0
```
![image](https://github.com/tektutor/chef-setup/assets/12674043/21265e6e-24ab-426e-bf1b-b1ec9bbdf2a1)
![image](https://github.com/tektutor/chef-setup/assets/12674043/11a0cb19-0ba1-478e-9b54-2fe90016e3b0)
![image](https://github.com/tektutor/chef-setup/assets/12674043/8c0aea82-5589-4528-a41f-d418e2647922)
![image](https://github.com/tektutor/chef-setup/assets/12674043/e7afcd2d-4964-4dae-8238-3c61ba043ee2)
![image](https://github.com/tektutor/chef-setup/assets/12674043/c4ed964e-b908-43b2-9cf1-9408a17c3e30)

We need to configure firewall on the Chef Automate Server to open up https port
```
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

We should be able to access the Chef Automate Webconsole/Dashboard
```
https://rhel-chef-automate
```
![image](https://github.com/tektutor/chef-setup/assets/12674043/1304900c-587d-4cb2-a06c-e5a81fc5ece4)

![image](https://github.com/tektutor/chef-setup/assets/12674043/51604607-ef53-4271-ac04-3c5618e42a5f)
![image](https://github.com/tektutor/chef-setup/assets/12674043/a7585f6f-8219-4026-81e5-897b460154ae)


We need to patch the Chef Automate server with the below patch.toml file
<pre>
[global.v1.external.automate]
enable = true
node = "https://rhel-chef-automate"
[global.v1.external.automate.auth]
token = "<data-collector token>"
[global.v1.external.automate.ssl]
server_name = "<server name from the automate server ssl cert>"
root_cert = """<pem format root CA cert>
"""
[auth_n.v1.sys.service]
# It is fine to use an A2 data collector token.
a1_data_collector_token = "<data-collector token>"
[erchef.v1.sys.data_collector]
enabled = true 
</pre>

![image](https://github.com/tektutor/chef-setup/assets/12674043/69531305-785d-4bfe-9e8d-a27904451dd1)

## Configuring Chef Infra Server to send data to external Chef Automate Server
We need to create a patch.toml configuration file as shown below
```


```
