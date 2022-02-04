---
layout: post
title: "BladeRF Setup Guide"
date: 2022-02-04
categories: sdr gnuradio bladerf
---

# BladeRF Setup Guide

I recently got a bladeRF micro A5 to play around with and everything is going wonderful so far. I did run into a few problems trying to get it setup though.

I started by following the guide on getting started on the bladeRF github page and since I am on Ubuntu 20.04 I followed the Linux instructions.

[Getting Started: Linux · Nuand/bladeRF Wiki](https://github.com/Nuand/bladeRF/wiki/Getting-Started%3A-Linux)

I wanted to use PyBOMBS because I knew in the future I wanted to use some of the out of tree modules for GNU Radio, and PyBOMBS is essentially a package manager for that. I think I also tried the ppa method because of reasons I’ll outline, but I eventually I got the PyBOMBS version working. 

The process I followed to get it working was to get the necessary packages as described

```bash
sudo apt-get install python3-six python-six python3-mako python3-lxml python3-lxml python3-numpy python3-numpy python3-pip git python3-pybind11 libsndfile1-dev
```

Then you can install PyBOMBS using pip. At first I was running into problems because I normally use anaconda to manage my python packages. Using anaconda appears to work at first but unless something has changed it will fail in some of the later steps.

```bash
pip3 install --upgrade git+https://github.com/gnuradio/pybombs.git
```

Now that PyBOMBS is install we auto config it and add the default recipes. According the PyBOMBS GitHub docs, the default recipe builds gnu radio 3.8

[GitHub - gnuradio/pybombs: PyBOMBS (Python Build Overlay Managed Bundle System) is the GNU Radio install management system for resolving dependencies and pulling in out-of-tree projects.](https://github.com/gnuradio/pybombs#recipes)

```bash
pybombs auto-config
pybombs recipes add-defaults
```

Then I created a directory to store all the GNU Radio components in. We also use an alias, bladeRF, for easy reference later when running commands, and install the default GNU Radio setup specified earlier.

```bash
mkdir ~/pybombs/
pybombs prefix init ~/pybombs/bladeRF -a bladeRF -R gnuradio-default
```

### /sidenote

That last command block is the one that I think was causing me trouble. The original set of commands is

```bash
mkdir ~/pybombs/
pybombs config --package gnuradio gitrev v3.9.0.0
pybombs config --package gr-osmosdr gitrev dev-gr-3.9
pybombs prefix init ~/pybombs/bladeRF -a bladeRF -R gnuradio-default
```

with the two lines in the middle setting specific versions for gnuradio and gr-osmosdr. I think these versions are incompatible with the software and simply removing the lines specifying the versions seems to do the trick.

### /end sidenote

Now we can install bladeRF (for controlling and loading the bitstream onto the bladeRF) and gqrx for viewing the waveform.

```bash
pybombs -p bladeRF install bladeRF gqrx
```

Once this is done you can run the bladeRF commandline interface with the normal usage by just prepending “pybombs -p bladeRF run <command>”

```bash
pybombs -p bladeRF run bladeRF-cli -- -i
```

To actually use the bladeRF, the bitstream has to be loaded onto the fpga every time it is powered on. Supposedly this can be done automatically but I haven’t figured that out yet.

To load the bitstream, first download the bitstream for your device from the site below

[FPGA - Nuand](https://www.nuand.com/fpga_images/)

Then navigate to the folder where it is located and run the following command changing the name of the file if yours is a different bladeRF.

```bash
pybombs -p bladeRF run bladeRF-cli -- -l hostedxA5-latest.rbf
```

The LEDs on the bladeRF should light up and it should show that the FPGA was successfully programmed on the command line. Below is the output when I run the command.

```shell
philip@PHILIP-DESKTOP:~$ pybombs -p bladeRF run bladeRF-cli -- -l hostedxA5-latest.rbf
[INFO] Prefix Python version is: 3.8.10
[INFO] PyBOMBS Version 2.3.5
Loading fpga...
Successfully loaded FPGA bitstream!
```

Now that the bladeRF FPGA is programmed you can run gqrx and see the spectrum

```bash
pybombs -p bladeRF run gqrx
```

## Credits
Written by: Philip Wig

Last updated: 2/4/2022