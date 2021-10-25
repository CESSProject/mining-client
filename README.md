
# CESS Miner Operation Manual



## Minimum system version

Ubuntu_x64 18.04

CentOS 8.2



## system configuration

### Ubuntu

##### Dependent package

```
$ sudo apt install util-linux ocl-icd-* wget curl geth -y
```

*If geth installation fails, use snap to install:

```
$ snap install --devmode geth
```

##### Open firewall port

If the firewall is turned on, you need to open the running port.

```
$ sudo ufw allow 15001:15010/tcp 
```



## CentOS

##### Dependent package

```
yum install vim git util-linux wget curl ocl-icd-* golang -y
```

Download the source code of Ethereum, compile and install geth:

```
$ git clone https://github.com/ethereum/go-ethereum.git
$ cd go-ethereum
$ make geth
```

##### Open firewall port

If the firewall is turned on, you need to open the running port.

```
$ sudo firewall-cmd --permanent --add-port=15001-15010/tcp
$ sudo firewall-cmd --reload
```



## Mount hard disk

Run the `fdisk -l` command to view the unmounted hard disk.

![image-20211021152646839](https://raw.githubusercontent.com/Cumulus2021/W3F-illustration/main/mining-client/img1.png)

If your disk is not found here, it indicates that the disk has not been successfully installed. Please shut down and reinstall the disk. This section uses `/dev/vdb` as an example to describe how to mount the disk.

```
$ fdisk /dev/vdb
```

![image-20211021152847523](https://raw.githubusercontent.com/Cumulus2021/W3F-illustration/main/mining-client/img2.png)

As shown in the figure above, enter n, p, 1, 2048, 1048575999, and w in the red box respectively. Note that 1048575999 must be the same as the size of your own disk. You can see the value of your disk before you enter it, as shown in the red line in the figure above.

```
$ mkfs.ext4 /dev/vdb
```

If you encounter the following prompt, enter y to continue:

```
Proceed anyway? (y,N) y
```

```
$ mkdir /test
$ echo "/dev/vdb /test ext4 defaults 0 0" >> /etc/fstab
```

Note that:` /dev/vdb` should be replaced with your own disk name, and `/test` should be consistent with the directory created in the previous step.

```
$ mount -a
```



## Parameter Files

Go to CESS website: https://cess.cloud/FAQ Article 11 download the parameters compression package, which stores the parameter files used to generate the proof, decompress these parameter files, Put it in the `/usr/cess-proof-parameters/` directory of the mining machine. If there is no such directory, run the following command to create it:

```
$ cd /usr/
$ mkdir cess-proof-parameters
```

Download and unzip the parameter file into the `/usr/cess-proof-parameters` directory：

```
$ wget https://d2gxbb5i8u5h7r.cloudfront.net/parameterfile.zip
$ unzip -d /usr/cess-proof-parameters parameterfile.zip
$ cd /usr/cess-proof-parameter
$ mv parameterfile/v28-* .
```



## Enable ICMP

Check whether ICMP is enabled:

```
$ cat /proc/sys/net/ipv4/icmp_echo_ignore_all
0
```

0 means enabled, 1 means disabled, edit `/etc/sysctl.conf`

```
$ vim /etc/sysctl.conf
```

Add content at the end of the file:

```
net.ipv4.icmp_echo_ignore_all=0
```

Then save and exit, and enter the following command to have the system load the configuration immediately:

```
$ sysctl -p
```



## Wallet & Staking

**1.** Install Google browser, install METAMASK plug-in, register METAMASK account, and create two METAMASK accounts:

Account 1 Wallet address: used for staking, revenue, penalty (private key user manages himself)

Account 2 Wallet address: for authentication, contract operations (private key provided in keystore file)

**2.** Get BNB of the test network from Binance smart chain faucet to pay gas fees for contract operation, and get the address:

 https://testnet.binance.org/faucet-smart 

Note: Please enter the wallet address for account 

**3.** Connect Metamask to the BSC test network, refer to https://cess.cloud/FAQ Article 2.

**4.** Add tokens to the wallet of account 1, refer to https://cess.cloud/FAQ article 3.

**5.** To the tap of the CESS chain to get the CES of the test net, for staking, to get the address:

https://www.cess.cloud/faucet 

Note: Please enter the wallet address for account 1.

**6.** Enter the CESS test system in BSC through Google Chrome at https://cess.cloud/ ,Click`CONNECT WALLET`on upper right corner, then select `Miner Login`. Enter Wallet 2 address and public IP, choose storage size , then add staking and click the button `Pledge immediately`. 



## keystore file

Create a keystore file with the wallet address of account 2, export the private key of  account 2 in metamask, create a priv file with touch priv in node directory, and write the  private key to the priv file. Finally, use geth to generate the keystore file:

```
$ geth account import ./priv
```

The generation process will prompt you to enter the password twice, so remember the  password.

View the location of the generated keystore file: 

```
$ geth account list
```



## Operation mining

* Go to CESS FAQ at https://cess.cloud/FAQ Under #11, download CESS `mining client`.

* View help information for the node program

```
$ sudo chmod +x ./node
$ sudo ./node -h 
Mining client of CESS. 
 
Usage: 
 node [options] [parameter] 
 
Example: 
 node -k xxx -p xxx -n xxx -i x.x.x.x:15001 -m / -s 500 
 
Options: 
 -h help 
 help information. 
 -i ip 
 Your public ip address and port. 
 -k keystore 
 The path to the keystore file. 
 -m Mount 
 Mount path of hard disk. 
 -n nodeId 
 Your nodeId. 
 -p password 
 Decryption password of keystore file. 
 -s space 
 Add space (In GB). 
 -v version 
 show type and version.
```

* Run node in the background: 

```
$ sudo ./node -k ./mykeystore -p xxx -m /data -n C123 -s 500 -i x.x.x.x:x > node.log  2>&1 & 
```

* Run node in the foreground: 

```
$ sudo ./node 
#########################################################################
### Mining client of CESS ###
### 1、You can run node in foreground or background mode ###
### 2、Background : nohup ./noed -k -p -n -i -m -s > node.log 2>&1 & ###
### 3、Foreground : ./node ###
### 4、You can run "./node -h" to see the command line arguments ###
#########################################################################
```

Enter the full path of the keystore file at wallet address 2:

```
1、Please enter your absolute filepath for the keystore file:
>./mykeystore
```

Enter the keystore unlock password: 

```
2、Please enter the keystore password: 
>*************
```

Enter the PeerID generated during mortgage:

```
3、Please enter the node ID returned when the mortgage is  successfule:
>C37
```

Enter the public IP address of the storage server:

```
4、Please enter your public ip:port:
NOTE: The default port is 15001. If you use an agent, please map the intranet port 15001. 
>xxxxxx:15001
```

Select a disk directory for storing files:

```
5、Below is your available storage space, please select one for  CESS storage:
NOTE: The mount directory must be kept consistent every time.
1) Mounted on: / Total: 1007GB Free: 623GB
2) Mounted on: /data Total: 4000GB Free: 3968GB
>2
```

Select the disk directory and total disk space to be reported.

```
6、Please choose whether to increase space for storage:
NOTE: Only when the accumulated additional space reaches 1TB can it work normally.
1) Not Increase  
2) 1GB  
3) 50GB  
4) 100GB  
5) 512GB  
6) 1TB  
7) 2TB  
8) 4TB  
9) 8TB  
10) 16TB  
>5
```

* After successful login verification, your reported space, verified space and used space  information will be displayed, and then storage mining officially begins:

```
###########################################################################  
# Hi xxx, please start your mining trip! 
# Reported:500GB, Verified:200789721088Bytes, Used:16777216Bytes. 
###########################################################################
```



