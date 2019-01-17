# openairinterface5G

Installation and Configuration of Radio Access Network: Open Air Interface (OAI) System Emulation Usage without S1.
* [Operating System and Kernal Requirements For RAN](#Operating-System-and-Kernal-Requirements)
* [OAI Power management](#OAI-Power-management)
* [Fetch, Install and Configure E-UTRAN](#Fetch-Install-and-Configure-E_UTRAN)

# Operating System and Kernal Requirements
Using the master branch in the OpenAirInterface Github repositories is recommended, it is important to install Ubuntu 14.04 LTS with low-latency kernel 3.19. because OpenAirInterface is very sensitive to version numbers of Linux kernel.
After downloading Ubuntu 14.04 from Ubuntu’s website then install it, besides low-latency Linux kernel version 3.19 is required.

* To find the version of your Ubuntu 

> `lsb_release -a`

* For Ubuntu 14.04.3 LTS (master branch)
> `sudo apt-get update`

> `sudo apt-get dist-upgrade`

> `sudo apt-get install linux-image-3.19.0-61-lowlatency linux-headers-3.19.0-61-lowlatency`
* For Ubuntu 16.04 LTS (develop branch)
> `sudo apt-get install linux-image-4.8.0-36-lowlatency linux-headers-4.8.0-36-lowlatency`

Rebooting your system is required after Low-latency kernel installation, then make sure the low-latency
kernel is successfully installed by Useing the following command in terminal:
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
> `sudo apt-get install cpufrequtils`

> `sudo echo GOVERNOR="performance"  > /etc/default/cpufrequtils`

> `sudo update-rc.d ondemand disable`

> `sudo /etc/init.d/cpufrequtils restart`

Install i7z utility to check the CPU to its frequency if it is changing then it is essential to fix the CPU issue before proceeding further. We will face the real- time issues with eNB/UE/RRH. Thus, The CPU should not change its frequency by more than 1-2 hertz and should not be any C-state other than C0.

> `sudo apt-get install i7z`

> `sudo i7z`

More information can be found in the OAI GitLab 

* [OAI - Openair kernel main setup](#https://gitlab.eurecom.fr/oai/openairinterface5g/wikis/OpenAirKernelMainSetup)

# Fetch, Install and Configure E_UTRAN
OpenAirInterface Github repositories is recommended, it is important to install Ubuntu 14.04 LTS with low-latency kernel 3.19. because OpenAirInterface is very sensitive to version numbers of Linux kernel.
