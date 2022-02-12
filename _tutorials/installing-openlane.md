---
layout: tutorial
title: Installing OpenLane
date: 2022-02-05
---

To install OpenLane you will need Linux in some fashion. Either spin up your favorite Linux distro on your computer or if you are stuck on Windows follow the instructions to install Windows Subsystem for Linux (WSL).

## Windows

### Installing WSL

To use OpenLane the easiest way is to use WSL. WSL stands for Windows Subsystem for Linux which allows you to run a Linux environment directly on Windows without much of the overhead of a traditional virtual machine. 

To install WSL follow the instructions on [this page](https://docs.microsoft.com/en-us/windows/wsl/install). (Basically just run `wsl --install` in PowerShell)

### Installing Docker

Docker will be used to create a container which contains all the necessary packages and dependencies that the OpenLane tools need. It is possible to install docker directly in WSL but I have found that it is easier to just install Docker Desktop on windows. Then you can even use Docker for windows development :). Follow the instructions [here](https://docs.docker.com/desktop/windows/install/) (Basically just download Docker Desktop for Windows and follow the installer prompts).

After installing docker you should be able to run ```docker -v``` in the WSL Linux command prompt and see a version number. The output when I run it is shown below for reference.

```shell
philip@PHILIP-DESKTOP:~$ docker -v
Docker version 20.10.7, build 20.10.7-0ubuntu5~20.04.2
```

## Linux

### Installing Docker

Follow the installation instructions [here](https://docs.docker.com/engine/install/ubuntu/). Make sure to select the installation instructions for your specific distro.

## Actually Installing OpenLane

If in doubt follow the installation instructions [here](https://openlane.readthedocs.io/en/latest/#table-of-contents). (You already installed Docker, need to install everything else)

If you have a brand new WSL install you need to install Python, make and some python packages. If you are running your own Linux distro you might already have some of these installed. Run the commands shown below in the Linux terminal (WSL or otherwise)

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install make python3 python3-pip -y
```

Then install the required python packages, Click and Pyyaml.

```bash
python3 -m pip install pyyaml click
```

Now clone the OpenLane repository to download it to your computer

```bash
git clone https://github.com/The-OpenROAD-Project/OpenLane.git
```

Change into the OpenLane directory and start setting up the PDK and OpenLane

```bash
cd OpenLane/
make pull-openlane
make pdk
```

The ```make pdk``` step can take a while to run so get a snack or take a brain break.

After it is finished and you are done with your snack, you can run the test to verify if everything was properly installed

```bash
make test
```

This should report success at the end of it. OpenLane is now installed!

## Extras

### WSL GUI

To open any of the GUI apps under WSL that are installed as part of OpenLane you need to configure a way to open GUI apps as WSL usually only provides a command line interface. Right now the two best ways that I've found are MobaXterm or WSLg. Choose **one** of these as they will not work at the same time. Both are using some sort of Xserver to be able to display the Linux GUI apps.

#### WSLg

For WSLg follow the WSLg installation instructions [here](https://github.com/microsoft/wslg) (basically making sure you have the right windows version) and everything should just work natively.

#### MobaXterm

Just install MobaXterm from [here](https://mobaxterm.mobatek.net/). Now just open a WSL command prompt and start the app that you want. Shown below is an example opening magic.

```bash
cd OpenLane/
make mount
magic
```

If there is an error about a display not being found then add the following to your .bashrc

```bash
export DISPLAY="$(/sbin/ip route | awk '/default/ { print $3 }'):0"
```

One way to do this is below

```bash
nano ~/.bashrc
```

Then scroll to the bottom using arrow keys (or mouse if it works) and paste the above export line to the end of the file. Then save and exit with "ctrl-x", "y", and "enter" (or read the prompts and see what those keypresses do).

Then you will need to source the new .bashrc for the change to take effect. Just restart the terminal or run the following command to source the .bashrc

```bash
source ~/.bashrc
```

### Errors

If docker gives you a permission denied error (seems to happen when the windows user is not an admin) all you need to do is run the make commands with sudo (or possibly start terminal with admin access, need to test)

```bash
sudo make mount
```

<!--
### Magic usage
To link the sky130 magic library into magic (not necessary for me, don't know why)
```bash
sudo ln -s pdks/sky130A/libs.tech/magic/* /usr/lib/x86_64-linux-gnu/magic/sys/
```

command taken and modified from [here](https://lootr5858.wordpress.com/2020/10/06/magic-vlsi-skywater-pdk-local-installation-guide/)

-->