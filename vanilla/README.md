Vanilla kernel cross compilation for BBB 
========

###Overview

1. Installing ARM cross compiler
2. Getting Universal bootloader for BBB
3. Cloning the vanilla kernel
4. Cross compilation of the kernel using Toolchain
5. Porting to BBB



## Step 1
To get started, first install the cross compiler for ARM to your machine

<pre><code>sudo apt-get install gcc-arm-linux-gnueabi</code></pre>

Install lzop package, the fastest compressor and decompressor. The kernel is compressed using LZO.

<pre><code>sudo apt-get install lzop</code></pre>

## Step 2

Now get the bootloader used on BBB called [u-boot](http://www.denx.de/wiki/U-Boot). It has special image format called uImage. Download u-boot and then compile and install u-boot tools.	

Download and untar it.
<pre><code>wget http://ftp.denx.de/pub/u-boot/u-boot-2013.10.tar.bz2
tar -xjf u-boot-2013.10.tar.bz2
cd u-boot-2013.10
make tools 
sudo install tools/mkimage /usr/local/bin
</pre></code>

## Step 3

Now clone the vanilla kernel (version - 3.8) from the repository

<pre><code>git clone https://github.com/beagleboard/kernel.git
cd kernel</pre></code>

Make it updated

<pre><code>git checkout 3.8-rt</pre></code>

Now run the shell script file.

<pre><codde>sudo ./patch.sh</pre></code>

Some further step

<pre><code>sudo cp configs/beaglebone kernel/arch/arm/configs/beaglebone_defconfig</pre></code>

Now download the AM33x CM3 power management firmware from Arago-project by Texas Instruments

<pre><code>wget http://arago-project.org/git/projects/?p=am33x-cm3.git\;a=blob_plain\;f=bin/am335x-pm-firmware.bin\;hb=HEAD -O kernel/firmware/am335x-pm-firmware.bin</pre></code>

<pre><code>cd kernel
pwd</pre></code>

Do verify you will have /kernel/kernel at the end 

## Step 4

This step includes cross compilation of the vanilla kernel.
<pre><code>sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- beaglebone_defconfig -j4</pre></code>
<pre><code>sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- uImage dtbs -j4</pre></code>
<pre><code>sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- uImage-dtb.am335x-boneblack -j4</pre></code>

Build kernel modules

<pre><code>sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- modules -j4</pre></code>

After successful compilation you will get the zImage file @/u-boot-2013.10/kernel/kernel/arch/arm/boot/zImage.
