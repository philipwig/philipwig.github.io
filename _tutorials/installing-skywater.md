---
layout: post
title: "SkyWater PDK Install Guide"
date: 2022-07-24
---

The Skywater PDK is a collaboration between Google and SkyWater to create an open-source 130nm PDK.
Unlike previously released PDKs, the skywater-pdk is for an actual manufacturable process.
The PDK consists of a variety of different devices and standard cells, which can be manually or programmatically used to create any sort of integrated circuit.

One problem with the pdk is how to install and manage not only the pdk itself, but the variety of tools that are needed to efficiently work with it.
I have documented below some of the different methods that I have used to get the skywater-pdk up and running.

I will note that personally I am much more into analog ic design, so all of the following tutorials are more focused on the analog side of the design process.
From what I understand, the [OpenLane](https://github.com/The-OpenROAD-Project/OpenLane) flow is the way to go if you want to do a digital design.

For all of the linux install I am using Ubuntu either natively or through WSL.

I will go over three ways of installing the skywater-pdk and tools on both windows and linux.

[My method](#method-1-python-script-install) uses a python script that I created that automatically installs the skywater pdk as well as some tools.
It also manages all of the versions of the individual tools to ensure repeatable installs.

Another way is to use a [docker container](#method-2-docker-install) created by Haral Pretel that contains the entire environment and everything already configured.

The [last way](#method-3-manual-install) is to install everything yourself.

## Method 1: Python Script Install

### Overview

This method can be used on linux or WSL.
Under linux you can choose to install everything in an lxc container (preferred method), or you can just install them natively.

If you are using WSL you can just install everything natively.

For both the linux install and WSL, I am using Ubuntu 22.04 but Ubuntu 20.04 or most other distros should work.
I have not tested them and you might have to modify the package install commands for your package manager.

### Linux LXC Install

Install lxd on your computer by using `sudo snap install lxd`.
This will install the latest version of lxd and lxc.

Clone the github repo and cd into it.

```bash
git clone https://github.com/philipwig/skywater-install.git
cd skywater-install
```

Now run the install script.
This script will create an lxc profile and container for the skywater pdk.
It will also install some default packages as well as Miniconda for managing python installs into the container.

```shell
./init-lxd.sh
```

To login to the container use the following command.

```shell
lxc exec skywater2 -- sudo --user philip --login
```

To install the pdk and tools, use the python script as shown below.
This will build and install all of the tools specified in the `setup/config.toml` file.
The tools will be installed into their default install locations or the `~/tools` directory.

```shell
python /remote/setup/skywater.py build
```

After the python script is done you should be ready to go!
All of the tools can be used like they were installed natively.
Run `xschem` to start xschem.

### WSL or Non-LXC Install

If you are using WSL or don't want to use lxc containers, you can use the python script on its own to manage your install.
You will need to have a recent python version installed (script tested with python 3.7 and above) and the necessary packages.

I usually use [Miniconda](https://docs.conda.io/en/latest/miniconda.html) to manage my python environments and packages.

If you are using miniconda, use the following command to install the environment.

```bash
conda env create --file environment.yaml
```

If you are not using conda, make sure you have python and the [toml](https://pypi.org/project/toml/) package installed.

Next clone the github repo and cd into it.

```bash
git clone https://github.com/philipwig/skywater-install.git
cd skywater-install
```

Now run the python script

```bash
python setup/skywater.py build
```

This will build and install all of the tools specified in the `setup/config.toml` file.
The tools be installed into their default install locations or the `~/tools` directory.

### Configuration

The versions of the installed tools can be configured by editing the `config.toml` file which contains the commit ids that will be installed.

If you want to update the tools versions to the latest versions run the following command.
The `-y` option can be appended to the command to automatically accept all of the changes.

```bash
python setup/skywater.py update
```

The tools specified in the `config.toml` will be installed in the order that they are listed there, so make sure that if any of the programs depend on another (open_pdks for example), that they are listed in the correct order.

All of the install scripts are located in the `setup/scripts` folder.
The python script will also create log and error files in each of the respective tools folders.
If there are errors during the install, check those files to see what went wrong.

You can also edit the install scripts if you want to customize your install.

To add another program to be installed, just create another folder with an install script named `install.sh`.
The python program passes three options into the script, first is the install directory, second is the git url, and the third is the git commit id.
Finally add the new program to the `setup/config.toml` file and the new program should be installed and managed along with all of the other tools.

### Extra

You can also use ssh to login, just get the ip address using `lxc list` and ssh into the container.
You will not be able to use any of the gui apps though as the xserver forwarding will not work.

```shell
(base) philip@PHILIP-DESKTOP:~$ lxc list
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
|   NAME    |  STATE  |         IPV4          |                     IPV6                      |   TYPE    | SNAPSHOTS |
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
| skywater  | RUNNING | 10.233.237.45 (eth0)  | fd42:96f2:eebf:5c2b:216:3eff:fe14:23bf (eth0) | CONTAINER | 0         |
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
(base) philip@PHILIP-DESKTOP:~$ ssh philip@10.233.237.45
(skywater) philip@skywater:~$ 
```

## Method 2: Docker install

You can also use docker to install and manage all of the tools.
I've found that I don't like how the docker container works as docker is really for running self contained programs and then exiting, not quite for running a desktop environment.
Harald Pretl has put together a really well done docker container that supports a variety of modes and has a ton of preinstalled tools.
You can check it out here.

[IIC-OSIC-TOOLS](https://github.com/hpretl/iic-osic-tools)

I usually like to use it in xserver mode as that interacts nicely with the native desktop.
One of the things I find annoying is that it is hard to customize the container.
Unlike a lxc container or a native install, you have to rebuild the docker image if you want to make any changes to it.

## Method 3: Manual Install

For this method I recommend using [open_pdks](http://opencircuitdesign.com/open_pdks/).
Open_pdks will install the skywater-pdk itself as well as some helpful libraries and scripts for using some of the other tools.

To install open_pdks you will need to have [magic](http://opencircuitdesign.com/magic/) installed.
Make sure that you compile the latest version from source yourself as the version installed with your package manager on linux will be out of date 99.99% of the time.

I also recommend installing Xschem and Ngspice.
Xschem is a schematic editor which is where you will create your circuits and Ngspice is the spice simulator to simulate the circuits.

You will also need some basic tools to compile and install these programs. Use the following command to install them if they arent installed already.

```shell
sudo apt install build-essential autoconf automake git make wget
```

### Magic Install

To install magic open the terminal and go to your favorite folder where you want to store magic.
I usually make a folder in my home directory `~/tools` to store all of my tools.
Then run the following commands to download and compile magic.

```shell
sudo apt install m4 tcsh csh libx11-dev tcl-dev tk-dev mesa-common-dev libglu1-mesa-dev
git clone https://github.com/RTimothyEdwards/magic.git
cd magic
./configure
make
sudo make install
```

Now magic should be installed.
If you run `magic`, it should open 2 windows. Congratulations, you have installed magic!

### open_pdks Install

To install open_pdks, run the following commands in your directory of choice (mine is `~/tools` again).

```shell
git clone git://opencircuitdesign.com/open_pdks
cd open_pdks
./configure --enable-sky130-pdk
make -j(nproc)
sudo make install
```

This will probably take a very long time as the pdk is quite large.
But once it is finished run the following line of code.

```shell
echo "export PDK_ROOT='/usr/local/share/pdk'" >> ~/.shellrc
```

This adds the `PDK_ROOT` variable to your shell so that programs know where the pdk is located on your computer.

### xschem Install

To install Xschem run the following commands.

```shell
sudo apt install libx11-6 libx11-dev libxrender1 libxrender-dev libxcb1 libx11-xcb-dev libcairo2 libcairo2-dev tcl tcl-dev tk tk-dev flex bison libxpm4 libxpm-dev gawk
git clone https://github.com/StefanSchippers/xschem.git
cd xschem
make
sudo make install
```

To point xschem to the open_pdks install, run the following command to create an xschem alias in your `.shellrc`.

```shell
echo "alias xschem='xschem --rcfile /usr/local/share/pdk/sky130A/libs.tech/xschem/xschemrc'" >> ~/.shellrc
```

The xschemrc file is the configuration for xschem.
You can modify this configuration to match your preferences but be sure to move it out of the default location so that it doesn't get overwritten if you ever update xschem.
Make sure to update the alias path in your `.shellrc` if you want it to point to a different file.
This can be done using any text editor (`nano` is my goto in the terminal) by finding the `alias xschem='xschem --rcfile {path to xschemrc}` line and editing the path to point to your customized xschemrc file.
For example if I placed my custom xschemrc file in the `~/tools` directory, the new alias would read, `alias xschem='xschem --rcfile ~/tools/xschemrc`.

### ngspice Install

The final piece of the basic puzzle is to install the simulator, ngspice.
Watch out for errors especially with the `./autogen.sh` command.
Sometimes that command fails the first time and just needs to be rerun.

```bash
sudo apt install bisonf flex libx11-dev libx11-6 libxaw7-dev autoconf automake libtool tcl tcl-dev tk tk-dev blt blt-dev libfftw3-3 libfftw3-dev libreadline-dev
git clone https://git.code.sf.net/p/ngspice/ngspice
cd ngspice
./autogen.sh
./configure --disable-debug --enable-openmp --with-x --with-readline=yes  --enable-xspice --with-fftw3=yes
make -j$(nproc)
sudo make install
```
