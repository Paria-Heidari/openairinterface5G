# openairinterface5G

Installation and Configuration of Radio Access Network: Open Air Interface (OAI) System Emulation Usage without S1.
* [Operating System and Kernel Requirements For RAN](#Operating-System-and-Kernal-Requirements)
* [OAI Power management](#OAI-Power-management)
* [Fetch, Install and Configure E-UTRAN](#Fetch-Install-and-Configure-E_UTRAN)
* [Running OAISIM without S1](#Running-OAISIM-without-S1)
* [New OpenAir LTE Emulation without S1](#New-OpenAir-LTE-Emulation-without-S1)
* [Openairinterface5g Structure](#Openairinterface5g-Structure)



# Operating System and Kernel Requirements
Using the master branch in the OpenAirInterface Github repository is recommended, it is important to install Ubuntu 14.04 LTS with low-latency kernel 3.19. because OpenAirInterface is very sensitive to version numbers of Linux kernel.
After downloading Ubuntu 14.04 from Ubuntu’s website then install it, besides low-latency Linux kernel version 3.19 is required.

* To find the version of your Ubuntu 

> `lsb_release -a`

* For Ubuntu 14.04.3 LTS (master branch)
> `sudo apt-get update`  
`sudo apt-get dist-upgrade`

> `sudo apt-get install linux-image-3.19.0-61-lowlatency linux-headers-3.19.0-61-lowlatency`
* For Ubuntu 16.04 LTS (develop branch)
> `sudo apt-get install linux-image-4.8.0-36-lowlatency linux-headers-4.8.0-36-lowlatency`

Rebooting your system is required after Low-latency kernel installation, then make sure the low-latency
kernel is successfully installed by Using the following command in terminal:
> `uname -a`

If you face some problems for installing Low-latency kernel go through the following youtube link:
* [Setting Kernel for Open Air Interface 5G User Equipment](https://www.youtube.com/watch?v=HKgVvwk-rSY)

# OAI Power management

Removing power management features in the Basic Input-Output System (BIOS) and Central Processing Unit (CPU) frequency scaling, also p-state and c-state in Linux must be disabled so we need to add GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_pstate=disable" to /etc/default/grub.

> `nano /etc/default/grub`

GRUB_CMDLINE_LINUX_DEFAULT ="quiet intel_pstate=disable"

Then perform the following command in the terminal

> `sudo update-grub`

Disabling CPU Frequency scaling is required, first Install cpufrequtils

> `sudo apt-get install cpufrequtils`

then update the Governor to performance
> `sudo echo GOVERNOR="performance"  > /etc/default/cpufrequtils`  
`sudo update-rc.d ondemand disable`  
`sudo /etc/init.d/cpufrequtils restart`

Install i7z utility to check the CPU to its frequency if it is changing then it is essential to fix the CPU issue before proceeding further. We will face the realtime issues with eNB/UE/RRH. Thus, The CPU should not change its frequency by more than 1-2 hertz and should not be any C-state other than C0.

> sudo apt-get install i7z  
sudo i7z

More information can be found in the OAI GitLab 

* [OAI - Openair kernel main setup](https://gitlab.eurecom.fr/oai/openairinterface5g/wikis/OpenAirKernelMainSetup)

# Fetch, Install and Configure E_UTRAN
The OpenAirInterface software can be obtained from their gitLab server. Check out RAN repository (eNB RAN + UE RAN) for Ubuntu 14.04 Get the master branch (Stable).

* For Ubuntu 14.04.3 LTS (master branch)

> `git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git`

The following important command permits to set up automatically the environment variables in the .bashrc. So it is unnecessary to set manually the environment variables.

> `cd openairinterface  
source oaienv` 

* to prevents problems during the compilation due to the variables declarations

> `cd cmake_targets  
./build_oai -I    # need to run only once`

The following command is a shell script that summarizes the compilation commands of the openairinterface5g code for different purposes.
> `./build_oai -h`

* to build the oaisim emulator without the S1 interface.
> `openairinterface5g /cmake_targets/  
./build_oai --oaisim --noS1 -c`

* The OAISIM executable is now located under this directory

> `openairinterface5g /cmake_targets/oaisim_noS1_build_oai/`

# Running OAISIM without S1


The important command is ”source init nas nos1” to load the nasmesh Kernel Module and Setup the OAI Interface.

> `cd cmake_targets/tools  
source init_nas_nos1`  

* Bring up oai0 Interface for eNB and oai1 interface 
> `sudo ifconfig oai0 10.0.1.1 netmask 255.255.255.0 broadcast 10.0.1.255`  
`sudo ifconfig oai1 10.0.2.2 netmask 255.255.255.0 broadcast 10.0.2.255`

* check the interfaces
> `ifconfig`

<pre>
> oai0    Link encap:AMPR NET/ROM  HWaddr 
          inet addr:10.0.1.1  Bcast:10.0.1.255  Mask:255.255.255.0
          UP BROADCAST RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0  
          collisions:0 txqueuelen:100 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

> oai1    Link encap:AMPR NET/ROM  HWaddr  
          inet addr:10.0.2.2  Bcast:10.0.2.255  Mask:255.255.255.0 
          UP BROADCAST RUNNING NOARP MULTICAST  MTU:1500  Metric:1  
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0  
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0  
          collisions:0 txqueuelen:100  
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
</pre>



* <b> Communicate between UE and eNB </b> 

* IP address for eNB 
> `10.0.1.1`  
* IP address for UE
> `10.0.2.2`

* to ping the UE or the eNB using the LTE link 
> `ping 10.0.1.2 # make eNB to ping UE (packets are generated at eNB)`  
`ping 10.0.2.1 # make UE to ping eNB`

After communication between UE and eNB, we use debug tools that is offered by OAI is graphical dashboard which shows the situation of channel response and signal power at the physical level in time and frequency domain.

> `cmake_targets/tools/run_enb_ue_virt_noS1 -x`


# New OpenAir LTE Emulation without S1
to build lte-softmodem-nos1 and lte-uesoftmodem-nos1 with a fronthaul protocol to communicate between UE and eNB, and IP Multicast over Ethernet. The following steps are needed to compile and run lte-softmodem and lte-uesoftmodem without S1.

More information for this new approach is in OAI GitLab:
* [OpenAir_LTE_Emulation_New](https://gitlab.eurecom.fr/oai/openairinterface5g/wikis/OpenAirLTEEmulationNEW)

There are some of them:

* <b> Building lte-softmodem-nos1 </b> 

* to build eNB

> `./build_oai --enb -t ETHERNET --noS1`

* <b> Running lte-softmodem-nos1 </b> 
Modifying a file in the ”targets/PROJECTS/GENERIC-LTE-EPC/CONF/YYY.conf” directory and it also possible to replace the loopback interface with a physical ethernet interface and the IP addresses to work on our network.

* to run eNB
<pre>
   source oaienv  
   cd cmake_targets/tools
   sudo -E ./init_nas_nos1 eNB
   cd ../lte_noS1_build_oai/build
   sudo -E ./lte-softmodem-nos1 -O YYY.conf
</pre>

* <b> Building lte-uesoftmodem-noS1 </b> 

* to build UE

> `./build_oai --UE -t ETHERNET --noS1`

# Openairinterface5g Structure

* <b>Openair1</b> folder includes the Channel simulation, and physical layer and the definition of its parameters, as well as an OAI user can emulate a real wireless system with the software included in this folder.  

* <b>Openair2</b> folder contains all the software that permits to implement the layer2 stack that includes Medium Access Control (MAC), Radio Link Control (RLC), Packet Data Convergence Protocol(PDCP) and the Radio Resource Control layer(RRC), and implementing all medium access protocols are allowed.  

* <b>Openair3</b> contains interfaces S1-C, S1-U (GTP, SCTP, S1AP) and NAS UE , all the modules related to the layer3.


