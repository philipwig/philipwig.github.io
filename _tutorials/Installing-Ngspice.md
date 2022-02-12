---
layout: tutorial
title: Installing Ngspice
date: 2022-02-11
---

This is a tutorial on how to install Ngspice in linux, specifically the Ubuntu distribution. I ran all of these commands on Ubuntu 20.04.3 LTS. You can see your distribution information using ```lsb_release -a```.

I will cover two different ways to install Ngspice. If you just want to install Ngspice and get up and running quickly, I would recommend following the [Quick Install](#quick-install) instructions. If you want to build from source the most up to date version, follow the instructions as [Building from Git](#building-from-git).

As of writing the current version installable with apt on Ubuntu Focal is 31.3-2 and the current release version is 36.

## Quick Install

To install from apt, run the following command.

```
sudo apt install ngspice
```

Thats it! Ngspice is now installed.

## Building from Git

To install from Git, I followed the instruction in Chapter 32 of the [Ngspice Manual](http://ngspice.sourceforge.net/docs/ngspice-36-manual.pdf#page=673). The manual details more configuration options if you want to get your hands dirty.

### Prerequisites

Some libraries are needed to compile Ngspice. They can be installed using apt if they are not already installed.

```
sudo apt install -y bison flex libx11-dev libx11-6 libxaw7-dev autoconf automake libtool libreadline8 libreadline-dev libfftw3-3 libfftw3-dev
```

### Building

Start by cloning the Ngspice repository.

```
git clone git://git.code.sf.net/p/ngspice/ngspice
```

Then navigate inside the folder and run the ```compile_linux.sh``` script. You might need to ```chmod +x``` the script to make it executable. ```compile_linux.sh``` needs to be run with sudo so that it can install Ngspice to the correct directories.

```
cd ngspice
chmod +x compile_linux.sh
sudo ./compile_linux.sh
```

Once that compiles sucessfully, Ngspice is installed!
