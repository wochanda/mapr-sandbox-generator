#mapr-sandbox-generator

Welcome to the MapR partner sandbox generator!  Here, you'll find instructions for creating a virtual machine image with the MapR Distribution for Hadoop and your application.

By creating your sandbox in this way, you'll have a repeatable process that can be leveraged each time you come out with a new version of your software, or MapR releases a new version.


###Step 1 - Get Packer
[Packer](www.packer.io) is a useful tool for automating the process of creating VM images, and it's what we use at MapR to create our sandbox.  To download and install it on a linux machine, do the following -
```
cd ~/
mkdir packer
cd packer
wget https://dl.bintray.com/mitchellh/packer/0.6.0_linux_amd64.zip
unzip 0.6.0_linux_amd64.zip
export PATH=$PATH:~/packer/
```

###Step 2 - Clone this repository
```
cd ~/
git clone git://github.com/wochanda/mapr-sandbox-generator
cd mapr-sandbox-generator
```

###Step 3 - Add files
In the mapr-sandbox-generator directory, you'll find files/, which stores any files you want to push to the VM when it is created.  This may be where you want to place startup scripts, tarballs, or packages.  Later you'll specify where in the VM these files are placed.

###Step 4 - Customize install script
In the mapr-sandbox-generator directory, you'll find a directory called script/.  Inside, there's a sample file called partner.sh, but you should create a new one called MyAppName.sh.  This script is run when you create your VM, so its where you'll want to place your installation routines.  This includes everything from downloading tarballs/rpms/packages, downloading them, setting up user accounts, and placing startup scripts.

###Step 5 - Download the MapR sandbox
Substitute the MapR version number in the command below.
```
mkdir output-virtualbox-ovf
wget http://package.mapr.com/releases/v3.1.1/sandbox/ova/MapR-Sandbox-For-Hadoop-3.1.1_VB.ova
```

###Step 6 - Customize install metadata
All of the information around how your VM gets created is in the partner-sandbox.json file.  The file is slightly self-explanatory, and for most installations you don't need to change much.  The only things you may need to change are in the "provisioners" section.  Here, you can add or remove files from getting copied into the new VM, or reference any new scripts you want to get run.  By default, it will try to pull /files/start_MyAppName.sh and /files/MyAppName.rpm into the VM, and run script/MyAppName.sh during setup.

###Step 7 - Create the VM
```
packer build --var 'mapr_version=3.1.1' --var 'partner_name=MyAppName' --var 'partner_version=7.0.1' partner-sandbox.json
```
