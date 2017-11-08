#################################################################
#	
#		RTL8814AU driver : EW-7833UAC AC1750 Wi-Fi USB 
#
#################################################################

Notes:

1. The Driver code is modified to make it compatible with the Buildroot.
2. The Driver code is forked from the Edimax source tree.
	please go to the following website to download the original src code : http://www.edimax.com/edimax/download/download/data/edimax/global/download/
3. Driver is fully compatiable with the linux version 4.4 and below. 
4. Make sure to download the correct Buildroot version and then compile it, refer to the below steps.


Buildroot compilation:
	a. Download the source code from the following link
		https://git.busybox.net/buildroot/refs/
	b. please use 2017.05.2 tag, as it uses linux kernel version 4.4, which is the lastest best compatible version for the driver code. 
	c. You can also try higher version kernel, at this point of time the driver code fails to compile with the latest kernel. 
	d. select the appropriate default confgs available in the configs folder which suits your hardware platform. 
	e. make "file name"
		I worked recently with the ATMEL SAMA5D2 Xplained rev.B Board, so i used "make atmel_sama5d2_xplained_mmc_defconfig" 
	f. vim .config 
	g. add the following lines in the .config file (not necessary, but enabling this will speed up your compilation). 
		1. BR2_JLEVEL=4 (assuming you have atleast 2 processor cores, if you have more, you can increase this number for faster compilation). 
		2. BR2_CCACHE=y. 
		3. If you want a Rootfs
			BR2_ROOTFS_OVERLAY=y
	h. make menuconfig 
		select all the target applications you might need for the buildroot, not necessarily required. 
	I. make linux-menuconfig
		select if anything required, not necessarily requried. 
	J. make

Driver compilation:

	a. Open the makefile in the parent directory.
	b. modify the parameters in the CONFIG_PLATFORM_BR section, as explained below
		ifeq ($(CONFIG_PLATFORM_BR), y)
		 // must need, but depends on your hardware mem architecture
		 EXTRA_CFLAGS += -DCONFIG_LITTLE_ENDIAN
		 // This flag should be enabled, enables the CFG80211 api's 
 		 EXTRA_CFLAGS += -DCONFIG_IOCTL_CFG80211 -DRTW_USE_CFG80211_STA_EVENT 
		 // select the arch based on your processor architecture
 		 ARCH := arm 
		 // point to the cross compilation tool chain, this will be available in the Buildroot directory. 
 		 CROSS_COMPILE := /home/***/buildroot-2017.05.2/output/host/usr/bin/arm-linux- 
		 //select the correct version of the linux version, it will be available in the buildroot directory
		 KVER ?= linux4sam_5.5 
		 //Point to the kernel source code, driver need a reference to the kernel headers. 
		 KSRC ?= /home/vchagari/Dev/Buildroot/OTAAntenna/buildroot-2017.08.1/output/build/linux-$(KVER)
		 //Directory where the driver source code is present. The compiled binaries will be avaiable in this directory.
		 MODDESTDIR := /home/vchagari/Dev/WiFi-Drivers/Realtek/Edimax-AC-1750/updated/EW7833UAC_linux_v4.3.21_17997.20160531/
		 endif
	c. Make sure power saving is disabled, CONFIG_POWER_SAVING = n. 
		I faced problems if it is enabled, After bringing up the wifi interface, 
		the modem comes up and then goes into a power saving mode, might be a bug. 
	d. If you want to work on another linux platform other than Buildroot, proceed the smiliar way. Add a label similar to CONFIG_PLATFORM_"***"=y.
		and add the lines mentioned in the step b. 

	e. do a make in the parent directory. 

