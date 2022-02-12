---
layout: tutorial
title: Installing Electric
date: 2022-02-11
---

Electric is written in Java and distributed as a .jar file which you should be able to run from anywhere on your computer. The following instructions will show one install method, feel free to modify for your use case. 

If you want to install Electric under Windows, follow the [Windows Install](#windows-install) instructions. If you are running Linux or have WSL installed on Windows, follow the [Linux/WSL Install](#linux/wsl-install) instructions.

## Windows Install

### Prerequisites

You will need to install Java on your computer. Just google "install java" or use this link [here](https://java.com/en/). This installed Java 1.8.0_321 for me. To check the installed java version, run ```java -version``` in a command prompt.

To enable the 3D display within Electric you will also need Java3D which can be downloaded [here](https://www.oracle.com/java/technologies/javase/java-3d.html). You should download Java3D for **amd64**. The file should end with **windows-amd64**. Run the installer and **note the install path**. If it is different than ```C:\Program Files\Java\Java3D\1.5.1``` you will need to change the ```JAVA3D_INSTALL_PATH``` variable in the batch file shown later to match the path on your machine.

### Installation

Create a folder named ```Electric``` somewhere on your machine. I put mine directly on my C:\ drive so the full path to the folder is ```C:\Electric``` .

Now download the **Electric binary** and **Static Free Software extras** from [here](https://www.staticfreesoft.com/productsFree.html). Make sure to chose the binary release of Electric. The source release just has extra stuff you probably don't need.

To enable Python scripting, download Jython [here](https://www.jython.org/download). I would recommend the Jython Standalone version so that it does not mess with any existing Python install as much.

Place all of the downloaded files into the Electric folder you created earlier. The folder should now contain electricBinary, electricSFS, and Jython .jar files. 

Now create a new .bat file in the same directory named ```electric.bat``` and paste the following command.

```
@echo OFF

set JAVA3D_INSTALL_PATH=C:\Program Files\Java\Java3D\1.5.1

PATH | find /i "%JAVA3D_INSTALL_PATH%\bin" > nul || set PATH=%JAVA3D_INSTALL_PATH%\bin;%PATH%

java -showversion -classpath "%JAVA3D_INSTALL_PATH%\lib\ext\j3dcore.jar;%JAVA3D_INSTALL_PATH%\lib\ext\j3dutils.jar;%JAVA3D_INSTALL_PATH%\lib\ext\vecmath.jar;jython-standalone-2.7.2.jar;electricSFS-9.07.jar;electricBinary-9.07.jar" com.sun.electric.Launcher -sdi
```

<!---
find and add to path line explanation
```
||   executes this command only if previous command's errorlevel is NOT 0
|    output of one command into the input of another command
>    output to a file
/i   ignores case
```
-->

```java``` runs Java, ```-showversion```  prints to the terminal the version of java that is being used, ```-classpath``` adds all of the .jar files that we want to run, ```com.sun.eletric.Launcher``` tells Java which class is the main one, ```-sdi``` makes each Electric window a separate window.

Now double click ```electric.bat``` to open electric.

To test if the plugins were loaded correctly, go to **Help -> About Electric -> Plugins**. You should see something like below

```
Java: version 1.8.0_321
CellRevisionPrivider: CellRevisionProvider
LayoutMergerFactor: Factory
Java3D: version 1.5.1 fcs (build6)
IRSIM: installed
Jython: installed
```

To test if Java3D is working, load the demo by going to **Help -> 3D Showcase -> Load Library**. Once the library is loaded go to **Help -> 3D Showcase -> 3D View of Cage Cell**. Now you should be able to see the 3D demo.

### Problems

If you get an error saying Java was not found, check if Java is in your path by opening a command prompt and running the following 

```
java -version
```

If Java is in your path, this should print out the installed version of Java. If it prints an error,  Java is not on your path. To fix, replace ```java``` in the above command with the **complete** path to the Java install on your computer. As an example, for my computer with the Java.exe path ```C:\Program Files (x86)\Common Files\Oracle\Java\javapath\java.exe```, the command would then become

```
"C:\Program Files (x86)\Common Files\Oracle\Java\javapath\java.exe" -showversion -classpath "%JAVA3D_INSTALL_PATH%\lib\ext\j3dcore.jar;%JAVA3D_INSTALL_PATH%\lib\ext\j3dutils.jar;%JAVA3D_INSTALL_PATH%\lib\ext\vecmath.jar;jython-standalone-2.7.2.jar;electricSFS-9.07.jar;electricBinary-9.07.jar" com.sun.electric.Launcher -sdi
```

## Linux/WSL Install

**For WSL users:** To install Electric under WSL you will need some way to display GUI apps like WSLg or an XServer installed under WSL. For WSLg follow the WSLg installation instructions [here](https://github.com/microsoft/wslg) (basically making sure you have the right windows version) and everything should just work natively. If you can't isntall WSLg for some reason, I've found that [MobaXterm](https://mobaxterm.mobatek.net/) usually works nicely. 

Start by installing Java using the following command

```
sudo apt install default-jre
```

You should now be able to run ```java --version``` and see your current Java version. For example, my output is shown below for reference.

```
philip@PHILIP-DESKTOP:~$ java --version
openjdk 11.0.13 2021-10-19
OpenJDK Runtime Environment (build 11.0.13+8-Ubuntu-0ubuntu1.20.04)
OpenJDK 64-Bit Server VM (build 11.0.13+8-Ubuntu-0ubuntu1.20.04, mixed mode, sharing)
```

Create a folder named ```Electric``` somewhere on your machine. I put mine directly in my home folder.

Now download the **Electric binary** and **Static Free Software extras** from [here](https://www.staticfreesoft.com/productsFree.html). Make sure to chose the binary release of Electric. The source release just has extra stuff you probably don't need.

To enable Python scripting, download Jython [here](https://www.jython.org/download). I would recommend the Jython Standalone version so that it does not mess with any existing Python install as much.

Place all of the downloaded files into the Electric folder you created earlier. The folder should now contain electricBinary, electricSFS, and Jython .jar files. 

Now download the Java3D installer for Linux from [here](https://www.oracle.com/java/technologies/javase/java-3d.html) into your Electric folder. Select the newest version of the Java3D API for linux-amd64.

```chmod +x``` the Java3D installer and run. It should install directly in the Electric folder. Now create a file named ```electric.sh``` and place the following in it.

```
BASEDIR=$(dirname "$0")

export LD_LIBRARY_PATH=$BASEDIR/lib/amd64

java -classpath $BASEDIR/electricBinary-9.07.jar:$BASEDIR/electricSFS-9.07.jar:$BASEDIR/jython-standalone-2.7.2.jar:$BASEDIR/lib/ext/j3dcore.jar:$BASEDIR/lib/ext/j3dutils.jar:$BASEDIR/lib/ext/vecmath.jar com.sun.electric.Launcher
```

Now ```chmod +x``` the electric.sh file and run it. Electric should open. To test if the plugins were loaded correctly, go to **Help -> About Electric -> Plugins**. You should see something like below

```
Java: version 11.0.13
CellRevisionPrivider: CellRevisionProvider
LayoutMergerFactor: Factory
Java3D: version 1.5.1 fcs (build6)
IRSIM: installed
Jython: installed
```

To test if Java3D is working, load the demo by going to **Help -> 3D Showcase -> Load Library**. Once the library is loaded go to **Help -> 3D Showcase -> 3D View of Cage Cell**. Now you should be able to see the 3D demo.

For convinence, you can add electric to your ```.bashrc``` so that you can launch it from anywhere. Add the following line to the end of your ```.bashrc```. Change the path if you installed Electric somewhere else.

```
alias electric="$HOME/electric/electric.sh"
```

Now you can just type ```electric``` into the terminal and it will launch using the current directory.

<!---
# Installing LTspice
Just go to [the LTspice page](https://www.analog.com/en/design-center/design-tools-and-calculators/ltspice-simulator.html) and download the version for you ([Download for Windows 7, 8, and 10 64-bit](https://ltspice.analog.com/software/LTspice64.exe) Version 17.0.32). Run the installer and install to the default directory. 

To increase the user experience of LTspice download the LTSpiceXVII.ini keyboard configuration and replace the default LTspiceXVII.ini file in the LTspice install directory. 

(TODO: Finish this tutorial)
-->
