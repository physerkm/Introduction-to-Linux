# **Introduction to Linux**

## **The Boot Process**

The Linux boot process is the procedure for initializing the system. It consists of everything that happens from when the computer power is first switched on until the user interface is fully operational. 

Having a good understanding of the steps in the boot process may help you with troubleshooting problems, as well as with tailoring the computer's performance to your needs. 

On the other hand, the boot process can be rather technical, and you can start using Linux without knowing all the details. 

_**NOTE:** You may want to come back and study this section later, if you want to first get a good feel for how to use a Linux system._

## **BIOS - The First Step**

Starting an x86-based Linux system involves a number of steps. When the computer is powered on, the **B**asic **I**nput/**O**utput **S**ystem (**BIOS**) initializes the hardware, including the screen and keyboard, and tests the main memory. This process is also called **POST** (**P**ower **O**n **S**elf **T**est).

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/bios.jpg" alt="bios"/>
</p>

The BIOS software is stored on a ROM chip on the motherboard. After this, the remainder of the boot process is controlled by the operating system (OS).

## **Master Boot Record (MBR) and Boot Loader**

Once the POST is completed, the system control passes from the BIOS to the **boot loader**. The boot loader is usually stored on one of the hard disks in the system, either in the boot sector (for traditional BIOS/MBR systems) or the **EFI** partition (for more recent (Unified) **E**xtensible **F**irmware **I**nterface or **EFI/UEFI** systems). Up to this stage, the machine does not access any mass storage media. Thereafter, information on date, time, and the most important peripherals are loaded from the CMOS values (after a technology used for the battery-powered memory store which allows the system to keep track of the date and time even when it is powered off).

A number of boot loaders exist for Linux; the most common ones are **GRUB** (for **GR**and **U**nified **B**oot loader), **ISOLINUX** (for booting from removable media), and **DAS U-Boot** (for booting on embedded devices/appliances). Most Linux boot loaders can present a user interface for choosing alternative options for booting Linux, and even other operating systems that might be installed. When booting Linux, the boot loader is responsible for loading the kernel image and the initial RAM disk or filesystem (which contains some critical files and device drivers needed to start the system) into memory.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/master%20boot%20record.jpg" alt="master boot record"/>
</p>

## **Boot Loader in Action**

The boot loader has two distinct stages:

For systems using the BIOS/MBR method, the boot loader resides at the first sector of the hard disk, also known as the **M**aster **B**oot **R**ecord (**MBR**). The size of the MBR is just 512 bytes. In this stage, the boot loader examines the **partition table** and finds a bootable partition. Once it finds a bootable partition, it then searches for the second stage boot loader, for example GRUB, and loads it into RAM (Random Access Memory). For systems using the EFI/UEFI method, UEFI firmware reads its Boot Manager data to determine which UEFI application is to be launched and from where (i.e. from which disk and partition the EFI partition can be found). The firmware then launches the UEFI application, for example GRUB, as defined in the boot entry in the firmware's boot manager. This procedure is more complicated, but more versatile than the older MBR methods.

The second stage boot loader resides under `/boot`. A splash screen is displayed, which allows us to choose which operating system (OS) to boot. After choosing the OS, the boot loader loads the kernel of the selected operating system into RAM and passes control to it. Kernels are almost always compressed, so its first job is to uncompress itself. After this, it will check and analyze the system hardware and initialize any hardware device drivers built into the kernel.

## **Initial RAM Disk**

The **initramfs** filesystem image contains programs and binary files that perform all actions needed to mount the proper root filesystem, like providing kernel functionality for the needed filesystem and device drivers for mass storage controllers with a facility called **udev** (for **u**ser **dev**ice), which is responsible for figuring out which devices are present, locating the device drivers they need to operate properly, and loading them. After the root filesystem has been found, it is checked for errors and mounted.

The **mount** program instructs the operating system that a filesystem is ready for use, and associates it with a particular point in the overall hierarchy of the filesystem (the **mount point**). If this is successful, the initramfs is cleared from RAM and the init program on the root filesystem (`/sbin/init`) is executed.

**init** handles the mounting and pivoting over to the final real root filesystem. If special hardware drivers are needed before the mass storage can be accessed, they must be in the initramfs image.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/the%20initial%20ram%20disk.jpg" alt="the initial ram disk"/>
</p>

## **Text-Mode Login**

Near the end of the boot process, **init** starts a number of text-mode login prompts. These enable us to type our username, followed by our password, and to eventually get a command shell. However, if we are running a system with a graphical login interface, we will not see these at first.

The terminals which run the command shells can be accessed using the **ALT** key plus a `function` key. Most distributions start six text terminals and one graphics terminal starting with **F1** or **F2**. Within a graphical environment, switching to a text console requires pressing **CTRL-ALT** + the appropriate function key (with **F7** or **F1** leading to the GUI).

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/text-mode%20logins.jpg" alt="text mode logins"/>
</p>

Usually, the default command shell is **bash** (the **GNU** **B**ourne **A**gain **Sh**ell), but there are a number of other advanced command shells available. The shell prints a text prompt, indicating it is ready to accept commands; after the user types the command and presses `Enter`, the command is executed, and another prompt is displayed after the command is done.

## **The Linux Kernel**

The boot loader loads both the **kernel** and an initial RAM–based file system (initramfs) into memory, so it can be used directly by the kernel.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/the%20linux%20kernel.jpg" alt="the linux kernel"/>
</p>

When the kernel is loaded in RAM, it immediately initializes and configures the computer’s memory and also configures all the hardware attached to the system. This includes all processors, I/O subsystems, storage devices, etc. The kernel also loads some necessary user space applications.

## **/sbin/init and Services**

Once the kernel has set up all its hardware and mounted the root filesystem, the kernel runs `/sbin/init`. This then becomes the initial process, which then starts other processes to get the system running. Most other processes on the system trace their origin ultimately to **init**; exceptions include the so-called kernel processes. These are started by the kernel directly, and their job is to manage internal operating system details.

Besides starting the system, **init** is responsible for keeping the system running and for shutting it down cleanly. One of its responsibilities is to act when necessary as a manager for all non-kernel processes; it cleans up after them upon completion, and restarts user login services as needed when users log in and out, and does the same for other background system services.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/sbin%2C%20init%20and%20services.jpg" alt="sbin init and services"/>
</p>

Traditionally, this process startup was done using conventions that date back to the 1980s and the System V variety of UNIX. This serial process had the system passing through a sequence of **runlevels** containing collections of scripts that start and stop services. Each runlevel supported a different mode of running the system. Within each runlevel, individual services could be set to run, or to be shut down if running.

However, all major distributions have moved away from this sequential runlevel method of system initialization, although they usually emulate many System V utilities for compatibility purposes. Next, we discuss the new methods, of which **systemd** has become dominant.

## **Startup Alternatives**

**SysVinit** viewed things as a serial process, divided into a series of sequential stages. Each stage required completion before the next could proceed. Thus, startup did not easily take advantage of the _**parallel processing**_ that could be done on multiple processors or cores.

Furthermore, shutdown and reboot was seen as a relatively rare event; exactly how long it took was not considered important. This is no longer true, especially with mobile devices and embedded Linux systems. Some modern methods, such as the use of **containers**, can require almost instantaneous startup times. Thus, systems now require methods with faster and enhanced capabilities. Finally, the older methods required rather complicated startup scripts, which were difficult to keep universal across distribution versions, kernel versions, architectures, and types of systems. The two main alternatives developed were:

**Upstart**

- Developed by Ubuntu and first included in 2006
- Adopted in Fedora 9 (in 2008) and in RHEL 6 and its clones

**systemd**

- Adopted by Fedora first (in 2011)
- Adopted by RHEL 7 and SUSE 
- Replaced Upstart in Ubuntu 16.04

While the migration to **systemd** was rather controversial, it has been adopted by all major distributions, and so we will not discuss the older System V method or Upstart, which has become a dead end. Regardless of how one feels about the controversies or the technical methods of **systemd**, almost universal adoption has made learning how to work on Linux systems simpler, as there are fewer differences among distributions. We enumerate **systemd** features next.

## **systemd Features**

Systems with **systemd** start up faster than those with earlier **init** methods. This is largely because it replaces a serialized set of steps with aggressive parallelization techniques, which permits multiple services to be initiated simultaneously.

Complicated startup shell scripts are replaced with simpler configuration files, which enumerate what has to be done before a service is started, how to execute service startup, and what conditions the service should indicate have been accomplished when startup is finished. One thing to note is that `/sbin/init` now just points to `/lib/systemd/systemd`; i.e. **systemd** takes over the **init** process.

One **systemd** command (`systemctl`) is used for most basic tasks. While we have not yet talked about working at the command line, here is a brief listing of its use:

- Starting, stopping, restarting a service (using **httpd**, the Apache web server, as an example) on a currently running system:
`$ sudo systemctl start|stop|restart httpd.service`

- Enabling or disabling a system service from starting up at system boot:
`$ sudo systemctl enable|disable httpd.service`

In most cases, the **`.service`** can be omitted. There are many technical differences with older methods that lie beyond the scope of our discussion.  

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/systemd.png" alt="systemd"/>
</p>

## **Linux Filesystems**

Think of a refrigerator that has multiple shelves that can be used for storing various items. These shelves help you organize the grocery items by shape, size, type, etc. The same concept applies to a filesystem, which is the embodiment of a method of storing and organizing arbitrary collections of data in a human-usable form.

Different types of filesystems supported by Linux:

- Conventional disk filesystems: `ext3`, `ext4`, `XFS`, `Btrfs`, `JFS`, `NTFS`, `vfat`, `exfat`, etc.
- Flash storage filesystems: `ubifs`, `jffs2`, `yaffs`, etc.
- Database filesystems
- Special purpose filesystems: `procfs`, `sysfs`, `tmpfs`, `squashfs`, `debugfs`, `fuse`, etc.
- 
This section will describe the standard filesystem layout shared by most Linux distributions.

## **Partitions and Filesystems**

A **partition** is a physically contiguous section of a disk, or what appears to be so in some advanced setups.

A **filesystem** is a method of storing/finding files on a hard disk (usually in a partition). 

One can think of a partition as a container in which a filesystem resides, although in some circumstances, a filesystem can span more than one partition if one uses symbolic links, which we will discuss much later.

A comparison between filesystems in Windows and Linux is given in the accompanying table:

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/partitions%20and%20filesystems.png" alt="partitions and filesystems"/>
</p>

## **The Filesystem Hierarchy Standard**

Linux systems store their important files according to a standard layout called the **F**ilesystem **H**ierarchy **S**tandard (**FHS**), which has long been maintained by the Linux Foundation. For more information, take a look at the following document: "_Filesystem Hierarchy Standard_" created by LSB Workgroup. Having a standard is designed to ensure that users, administrators, and developers can move between distributions without having to re-learn how the system is organized.

Linux uses the '`/`' character to separate paths (unlike Windows, which uses '`\`'), and does not have drive letters. Multiple drives and/or partitions are mounted as directories in the single filesystem. Removable media such as USB drives and CDs and DVDs will show up as mounted at `/run/media/yourusername/disklabel` for recent Linux systems, or under `/media` for older distributions. For example, if your username is **`student`** a USB pen drive labeled FEDORA might end up being found at `/run/media/student/FEDORA`, and a file `README.txt` on that disc would be at `/run/media/student/FEDORA/README.txt`.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/the%20filesystem%20hierarchy%20standard.jpg" alt="the filesystem hierarchy standard"/>
</p>

## **More About the Filesystem Hierarchy Standard**

All Linux filesystem names are case-sensitive, so `/boot`, `/Boot`, and `/BOOT` represent three different directories (or folders). Many distributions distinguish between core utilities needed for proper system operation and other programs, and place the latter in directories under `/usr` (think user). To get a sense for how the other programs are organized, find the `/usr` directory in the diagram from the previous page and compare the subdirectories with those that exist directly under the system root directory (`/`).

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/more%20about%20the%20filesystem%20hierarchy%20standard.png" alt="more about the filesystem hierarchy standard"/>
</p>

## **Choosing a Linux Distribution**

Suppose you intend to buy a new car. What factors do you need to consider to make a proper choice? Requirements which need to be taken into account include the size needed to fit your family in the vehicle, the type of engine and gas economy, your expected budget and available financing options, reliability record and after-sales services, etc.

Similarly, determining which distribution to deploy also requires planning. The figure shows some, but not all choices. Note that many embedded Linux systems use custom crafted contents, rather than Android or Yocto.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/choosing%20a%20linux%20distribution.png" alt="choosing a linux distribution"/>
</p>

## **Questions to Ask When Choosing a Distribution**

Some questions worth thinking about before deciding on a distribution include:

- What is the main function of the system (server or desktop)?
- What types of packages are important to the organization? For example, web server, word processing, etc.
- How much hard disk space is required and how much is available? For example, when installing Linux on an embedded device, space is usually constrained.
- How often are packages updated?
- How long is the support cycle for each release? For example, **LTS** releases have long-term support.
- Do you need kernel customization from the vendor or a third party?
- What hardware are you running on? For example, it might be **X86**, **ARM**, **PPC**, etc.
- Do you need long-term stability? Can you accept (or need) a more volatile cutting edge system running the latest software?


## **Graphical Desktop**

You can use either a **C**ommand **L**ine **I**nterface (**CLI**) or a **G**raphical **U**ser **I**nterface (**GUI**) when using Linux. To work at the CLI, you have to remember which programs and commands are used to perform tasks, and how to quickly and accurately obtain more information about their use and options. On the other hand, using the GUI is often quick and easy. It allows you to interact with your system through graphical icons and screens. For repetitive tasks, the CLI is often more efficient, while the GUI is easier to navigate if you do not remember all the details or do something only rarely. 

We will learn how to manage sessions using the GUI for the three Linux distribution families that we cover the most in this course: Red Hat (CentOS, Fedora), SUSE (openSUSE), and Debian (Ubuntu, Mint). Since we are using the GNOME-based variant of openSUSE rather than the KDE-based one, all are actually quite similar. If you are using KDE (or other Linux desktops such as XFCE), your experience will vary somewhat from what is shown, but not in any intrinsically difficult way, as user interfaces have converged to certain well-known behaviors on modern operating systems. In subsequent sections of this course we will concentrate in great detail on the command line interface, which is pretty much the same on all distributions.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/ubuntu%2C%20centos%2C%20and%20opensuse%20desktops.png" alt="ubuntu, centos, and opensuse desktops"/>
</p>

## **X Window System**

Generally, in a Linux desktop system, the X Window System is loaded as one of the final steps in the boot process. It is often just called X.

A service called the **Display Manager** keeps track of the displays being provided and loads the X server (so-called, because it provides graphical services to applications, sometimes called X clients). The display manager also handles graphical logins and starts the appropriate desktop environment after a user logs in.

X is rather old software; it dates back to the mid 1980s and, as such, has certain deficiencies on modern systems (for example, with security), as it has been stretched rather far from its original purposes. A newer system, known as [Wayland](https://wayland.freedesktop.org/), is gradually superseding it and is the default display system for Fedora, RHEL 8, and other recent distributions.  For the most part, it looks just like X to the user, although under the hood it is quite different.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/display%20manager.jpg" alt="display manager"/>
</p>

## **More About X**

A desktop environment consists of a session manager, which starts and maintains the components of the graphical session, and the window manager, which controls the placement and movement of windows, window title-bars, and controls.

Although these can be mixed, generally a set of utilities, session manager, and window manager are used together as a unit, and together provide a seamless desktop environment.

If the display manager is not started by default in the default runlevel, you can start the graphical desktop different way, after logging on to a text-mode console, by running **`startx`** from the command line. Or, you can start the display manager (**gdm**, **lightdm**, **kdm**, **xdm**, etc.) manually from the command line. This differs from running **`startx`** as the display managers will project a sign in screen. We discuss them next.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/desktop%20environment.jpg" alt="desktop environment"/>
</p>

## **GUI Startup**

When you install a desktop environment, the X display manager starts at the end of the boot process. It is responsible for starting the graphics system, logging in the user, and starting the user’s desktop environment. You can often select from a choice of desktop environments when logging in to the system.

The default display manager for GNOME is called **gdm**. Other popular display managers include **lightdm** (used on Ubuntu before version 18.04 LTS) and **kdm** (associated with KDE).

## **GNOME Desktop Environment**

GNOME is a popular desktop environment with an easy-to-use graphical user interface. It is bundled as the default desktop environment for most Linux distributions, including Red Hat Enterprise Linux (RHEL), Fedora, CentOS, SUSE Linux Enterprise, Ubuntu and Debian. GNOME has menu-based navigation and is sometimes an easy transition to accomplish for Windows users. However, as you will see, the look and feel can be quite different across distributions, even if they are all using GNOME.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/gnome.jpg" alt="gnome"/>
</p>

Another common desktop environment very important in the history of Linux and also widely used is KDE, which has often been used in conjunction with SUSE and openSUSE. Other alternatives for a desktop environment include Unity (present on older Ubuntu, but still based on GNOME), XFCE and LXDE. As previously mentioned, most desktop environments follow a similar structure to GNOME, and we will restrict ourselves mostly to it to keep things less complex.

## **Network Configuration**

All Linux distributions have network configuration files, but file formats and locations can differ from one distribution to another. Hand editing of these files can handle quite complicated setups, but is not very dynamic or easy to learn and use. **Network Manager** was developed to make things easier and more uniform across distributions. It can list all available networks (both wired and wireless), allow the choice of a wired, wireless, or mobile broadband network, handle passwords, and set up Virtual Private Networks (VPNs). Except for unusual situations, it is generally best to let Network Manager establish your connections and keep track of your settings.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/network%20configuration.jpg" alt="network configuration"/>
</p>

## **Wired and Wireless Connections**

Wired connections usually do not require complicated or manual configuration. The hardware interface and signal presence are automatically detected, and then Network Manager sets the actual network settings via **D**ynamic **H**ost **C**onfiguration **P**rotocol (**DHCP**).

For **static** configurations that do not use DHCP, manual setup can also be done easily through Network Manager. You can also change the Ethernet **M**edia **A**ccess **C**ontrol (**MAC**) address if your hardware supports it. The MAC address is a unique hexadecimal number of your network card.

<p align="center">
  <img src="https://github.com/physerkm/Introduction-to-Linux/blob/main/wired%20and%20wireless%20connections.jpg" alt="wired and wireless connections"/>
</p>

Wireless networks are usually not connected by default. You can view the list of available wireless networks and see which one (if any) you are currently connected to by using Network Manager. You can then add, edit, or remove known wireless networks, and also specify which ones you want connected by default when present.


















