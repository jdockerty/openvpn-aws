# OpenVPN using AWS EC2 - RHEL instance.
RHEL was chosen for learning purposes as I suspected it would be a more cumbersome configuration and a chance to become more comfortable with RH, although in hindsight it was not too obnoxious, thanks to the use of a [shell script to handle the OpenVPN setup.](https://github.com/Nyr/openvpn-install) My assumption is that this will also work on CentOS, although this has not been tested.

### AWS Setup

Firstly, select the RHEL instance.

![RHELInstanceEC2](https://github.com/jdockerty/openvpnaws/blob/master/Images/selectingEC2.png)

Ensure that you are using the free-tier instance, although if you wish to use a larger instance then you may. Move through the steps until you reach the 'Security Group' and then apply the rules below.

![SGConfig](https://github.com/jdockerty/openvpnaws/blob/master/Images/configuringSG.png)

* SSH is used for the configuration of the instance once it is running.
* The custom UDP rule on port 1194 is for the OpenVPN configuration.

Generate a key pair if you do not have one or use an existing one, then launch the instance.

Allocate and associate an Elastic IP (EIP) from AWS to the running EC2 instance. This ensures that the IP address remains static for the machine.

### OpenVPN Setup

Ensure you have installed the [OpenVPN GUI client](https://openvpn.net/community-downloads/) on your personal machine before SSHing into the AWS instance.

Once you are at the terminal of the instance, run the following commands or copy and paste.

```
sudo dnf -y install git -y
sudo dnf install iptables -y
sudo dnf install wget -y
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y
git clone https://github.com/Nyr/openvpn-install.git
cd openvpn-install/
chmod +x openvpn-install.sh
sudo ./openvpn-install.sh
```
Briefly, this is doing the following:
* Installing git, used in the git clone for retreiving the shell script from the GitHub repo.
* Installing iptables, used for setting routing rules for the VPN.
* Installing wget to retrieve content via the web, used within the shell script.
* Installing an RPM package for the epel-release repo.
* Retreiving the shell script from Nyr's git repo.
* Move into the directory of the downloaded repo files.
* Provide the shell script with exectuable permissions.
* Run the shell script.

From running the script, you are prompted with the relevant options for the OpenVPN installation.

The script will place an `<your_client_name>.ovpn` file in the `/root/` directory. The contents of this can be copied into a file with the `.ovpn` extension on your actual machine (e.g. I copied it into a file on my Win10 PC at home), it can be read with `sudo cat /root/yourfilename.ovpn`.

The installation can be verified by checking whether a tunnel instance has been configured. This can be done with 

`ip addr | grep tun0` 

Which should display something similar to below.

![tunnelinterface](https://github.com/jdockerty/openvpnaws/blob/master/Images/tunnel%20grep.png)

Once this is done, ensure that you have the OpenVPN GUI installed on your PC and load the generated configuration file, the `.ovpn` you copied, into the OpenVPN client. From here it should connect and the setup is complete.
