# Crosscompiling

### Intro

Unxmaal and onre have worked together to give us a nice and easy method to crosscompile programs for IRIX. Unxmaal's "compilertron" utilizes a patched GCC 4.7.4 in order to build a nice crosscompilation environment in a Vagrant Debian virtual machine.

Crosscompilation brings a number of advantages including vastly improved compilation times.

### Setting up compilertron

* Make sure you have Vagrant, Virtualbox, and Git
* Clone Unxmaal's compilertron repo - `git clone https://github.com/unxmaal/compilertron`
* Go into the directory and run `vagrant up` - the magic of Vagrant will provision the virtual machine for you!
* After everything is completed, you can access the virtual machine by running `vagrant ssh`
* `sudo -i` to root
* root bashrc:
You might want to make a .bashrc for root in order to set the proper PATH, CC, and CXX variables. Here is an example:
```
export PATH=/opt/binutils/bin:/opt/gcc/bin:$PATH
export CC=mips-sgi-irix6.5-gcc
export CXX=mips-sgi-irix6.5-g++
```
* Congratulations - you should now have a basic crosscompilation environment set up!
