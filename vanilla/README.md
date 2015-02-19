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

## Step 5

In this step you can now port the built kernel to the BBB.

First of all make directory 

<pre><code> sudo mkdir /home/'YOUR_USERNAME'/export
sudo mkdir /home/'YOUR_USERNAME'/export/rootfs</pre></code>
 replace ' ' with your username.

If your [rootfs] (www.kernel.org/doc/Documentation/filesystems/ramfs-rootfs-initramfs.txt) is ready .

<pre><code>make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- INSTALL_MOD_PATH=/home/'YOUR_USERNAME'/export/rootfs modules_install</pre></code>

Now we will install our cross compiled vanilla kernel.

First you need to download [debian](http://debian.beagleboard.org/images/BBB-eMMC-flasher-debian-7.5-2014-05-14-2gb.img.xz).

Now insert your sd card into PC. Depending upon your running OS follow the procedure.

Windows OS:- Usiing 7 zip first unzip the downloaded file and you will have .img file. Use [win32](http://sourceforge.net/projects/win32diskimager/files/latest/download) to install the image file on the sd card.

Linux OS:- First of all find the mount point of the sd card. For that before inserting sd card write

<pre><code>df -h</pre></code>
Now insert your sd card. Again write
<pre><code>df -h</pre></code>

Now see what drive is added use that drive in further command.

Be careful before executing next line. Otherwise hd can be wiped.
<pre> <code>xzcat BBB-eMMC-flasher-debian-7.1-2013-10-08.img.xz | dd of=/dev/sd'' bs=1M</pre></code>
replace ' ' with your mount point.

Now remove your sd card and insert to BBB without turning on power.

Then press boot push button and power on BBB and remains pushed until all LEDs are constant blue. Then sit back until the BBB power off automatically. So now kernel has booted up and ready to use BBB.

Connect BBB to network and find its IP address.
<pre><code>ssh root@'192.IP address'</pre></cpode>

root login doesn't require psswword. Now
<pre><code>cd /boot/uboot/</pre></code>
Create backup directory and do backup current kernel
<pre><code>mkdir backup
cp zImage uInitrd backup/</pre></code>
<pre><code>ls /lib/modules</pre></code>
It will display 3.8.13-bone28 or similar that is current kernel

Now go to our Ubuntu computers terminal and execute
<pre><code>cd /home/'YOUR_USER_NAME'/export/rootfs/lib/modules</pre></code>
Execute following commands to install vanilla kernel to BBB
<pre><code>sudo rsync -avz 3* root@192.168.'1.76':/lib/modules/</pre></code>
<pre><code>sudo rsync /home/'proficnc'/u-boot-2013.10/kernel/kernel/arch/arm/boot/zImage root@192.168.1.3 :/boot/uboot/</pre></code>

After successful execution go to BBB terminal and write
<pre><code> ls /lib/modules</pre></code>
Now you will have rt folder along with previous one.

now type
<pre><code>sync</pre></code>
<pre><code>reboot</pre></code>
 
