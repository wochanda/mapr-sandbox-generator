# A set of packer definition files (*.json) and some utility scripts
# to aid in the generation of Sandbox environments for MapR.
#

#	Prerequisites 
#	-------------

Download and install appropriate flavor of "packer" package 
from http://www.packer.io/.   Make sure the utility is in your PATH.

Download and install VirtualBox for your platform from  
https://www.virtualbox.org/wiki/Downloads


#	Overview
#	--------

The VM generation is done in stages, so as to enable customization
at any point.   The stages are 
	1. Base : the operating system only
	2. Sandbox-Base : Base plus the initial installation of MapR Software
	3. Sandbox : Final VM version, with customizations

The input for the process is a packer definition file (usually a
json document).  The different phases are defined there.

Additional environment variables can also be specified in the .json 
file, for use during the execution of customization scripts.

For this version, separate variables are available for the different
MapR ecosystem components.  These variables are used during the
final sandbox step.
  - If the eco system package variable is set to empty string "" 
    then the latest packages are picked up
  - If the eco system pacakge variable is set to "0",  then the package 
    is not included in the final customized sandbox
  - If the eco system package variable is set to a non-empty, 
    non-"0" value, that specific version of the package is installed


#	Examples
#	--------

1. Generate a CentOS Base Image 
  `packer build --only=base --var 'mapr_hue_version=""' --var 'mapr_hbase_version=""' --var 'mapr_pig_version=""' --var 'mapr_oozie_version=0' --var 'mapr_hcatalog_version=0' --var 'mapr_flume_version=0' --var 'mapr_hive_version=0.12.24975-1' --var 'mapr_mahout_version=0' --var 'mapr_version=3.0.3' --var 'mapr_core_repo_url=http://package.mapr.com/releases' --var 'mapr_eco_repo_url=http://package.mapr.com/releases/ecosystem' mapr-sandbox.json`

2. Add the MapR core software to the vm created in step 1
  `packer build --only=sandbox-base --var 'mapr_hue_version=""' --var 'mapr_hbase_version=""' --var 'mapr_pig_version=""' --var 'mapr_oozie_version=0' --var 'mapr_hcatalog_version=0' --var 'mapr_flume_version=0' --var 'mapr_hive_version=0.12.24975-1' --var 'mapr_mahout_version=0' --var 'mapr_version=3.0.3' --var 'mapr_core_repo_url=http://package.mapr.com/releases' --var 'mapr_eco_repo_url=http://package.mapr.com/releases/ecosystem' mapr-sandbox.json`

3. Generate a fully functional Sandbox using the sandbox-base from step 2
  `packer build --only=sandbox --var 'mapr_hue_version=""' --var 'mapr_hbase_version=""' --var 'mapr_pig_version=""' --var 'mapr_oozie_version=0' --var 'mapr_hcatalog_version=0' --var 'mapr_flume_version=0' --var 'mapr_hive_version=0.12.24975-1' --var 'mapr_mahout_version=0' --var 'mapr_version=3.0.3' --var 'mapr_core_repo_url=http://package.mapr.com/releases' --var 'mapr_eco_repo_url=http://package.mapr.com/releases/ecosystem' mapr-sandbox.json`


Each step creates a new directory for the resultant Virtual Machine
image.


TBD : Document the Veritica Sandbox generation process more completely, 
since it deviates slightly from the logic below.

As of 11-May-2014, the process uses a modified packer file 
(vertica-sandbox.json) and is executed simply
	packer build --only=base --var "mapr_version=3.0.2" vertica-sandbox.json
	packer build --only=sandbox-base --var "mapr_version=3.0.2" vertica-sandbox.json
	packer build --only=vertica --var "mapr_version=3.0.2" vertica-sandbox.json


Two additional files are needed (default location /var/packages on
the build system ... can be changed in the packer definition file
    mapr-patch-3.0.2.22510.GA-25250.x86_64.rpm
    vertica-7.0.1-0.x86_64.RHEL5.rpm

VM Generation for Vertica
* (Same as above) Install packer
  Download and install appropriate package from http://www.packer.io/

* (Same as above) Install VirtualBox
  Download and install appropriate package from https://www.virtualbox.org/wiki/Downloads
  
* (Same as above) Generate a base image
  `packer build -only=base mapr-sandbox.json`

* (Same as above) Download the core file. This is an optimization step so we do not need
  to download it every time we build the package (Not needed for MapR 3.0.3)
  `wget -O mapr-core-3.1.0.x86_64.rpm http://package.mapr.com/releases/v3.1.0/redhat/mapr-core-3.1.0.23703.GA-1.x86_64.rpm`

* (Same as above) Generate a MapR Demo VM Image using the base image created in the first step
  `packer build -only=virtualbox-iso mapr-demovm.json`



* Download the Vertica RPM. You will need to log in to get the binary.
  https://my.vertica.com/download-community-edition/

* Name the binary vertica.rpm

* Generate a Vertica & MapR Demo VM Image using the base image created in the previous step
  `packer build -only=vertica mapr-vertica-demovm.json`

  
