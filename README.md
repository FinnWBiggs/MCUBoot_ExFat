# MCUBoot_ExFat

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

