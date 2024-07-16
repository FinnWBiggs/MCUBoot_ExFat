# MCUBoot_ExFat

## BEWARE!!!

To those who come after me, likely from my nordic devZone post, something is very wrong with how this repository builds. 

Every code and config file in this repo is indentical to a version I got working, but this one generates assembler errors. 

It's still linked in the devZone post, so I'm leaving this up rather than deleting it, but it WILL NOT BUILD.

The problem with partition manager and mass storage is well-covered in the following nordic devzone posts: 

* (mine -- you may have come from here) https://devzone.nordicsemi.com/f/nordic-q-a/112280/mcuboot-dfu-interference-with-exfat-filesystem
* (a clever person who found a PM bug) https://devzone.nordicsemi.com/f/nordic-q-a/109170/bug-in-partition_manager_output-py-leading-to-incorrect-pm_foreach_affiliated_to_disk
* (relevant pull request) https://github.com/nrfconnect/sdk-nrf/pull/16444
* (someone with a similar issue) https://devzone.nordicsemi.com/f/nordic-q-a/113000/usb-mass-sample-and-partition-manager-build-issues

I wish you the best of luck!

## Build Instructions

Nothing too special needs to be done to build this project.

When adding a build configuration through the nrfConnect SDK GUI, use the following options:

| Build Config      | Value                                 |
| ----              | ----                                  |
| SDK               | v2.5.0                                |
| Board             | nrf52840dk_nrf52840                   | 
| Configuration     | prj.conf                              |  
| Overlay           | boards\nrf52840dk_nrf52840.overlay    |
| Partitioning      | pm_static.yml                         |

## New, more minimal method to crash the code

NOTE: I have added one more prj.conf option -- REAL_MAIN

Setting REAL_MAIN to yes compiles the project with the main from the mass sample.
Setting REAL_MAIN to no compiles the project with a stub main.

In prj.conf, enable partition manager via

CONFIG_PM_SINGLE_IMAGE=y

One can enable build conflicts by activating "child" config options implied by CONFIG_APP_MSC_STORAGE_FLASH_FATFS=y

```
config APP_MSC_STORAGE_FLASH_FATFS
    bool "Use FLASH disk and FAT file system"
    imply DISK_DRIVER_FLASH
    imply FILE_SYSTEM
    imply FAT_FILESYSTEM_ELM
```
Of these options, only DISK_DRIVER_FLASH causes problems

Going one step further in:

```
config DISK_DRIVER_FLASH
	bool "Flash"
	select FLASH
	select FLASH_MAP
	help
	  Flash device is used for the file system.
```
By disabling DISK_DRIVER_FLASH, but enabling FLASH and FLASH_MAP, it is possible to build and flash, which gives the following error.

```
*** Booting nRF Connect SDK v2.5.0 ***
[00:00:00.254,241] <err> fs: fs mount error (-5)
[00:00:00.254,241] <err> main: Failed to mount filesystem
[00:00:00.254,425] <inf> main: The device is put in USB mass storage mode.
```

The behavior in this state is to present a USB drive, but as soon as it is clicked, my windows machine says "you need to format the disk in drive D:/ before you can use it."

Formatting the drive allows the filesystem to be used until reset, at which point its contents are lost.


## (Previous, Irrelevant) Initial Configuration Options to "Break" the Sample 

In prj.conf, the following options add and remove the conflicting subsystems

Filesystem is added by 
> CONFIG_APP_MSC_STORAGE_FLASH_FATFS=y

MCUBoot is added by 
> CONFIG_BOOTLOADER_MCUBOOT=y

When either one of these options is enabled the code crashes.

