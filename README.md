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

Then, in prj.conf, the following options add and remove the conflicting subsystem

Filesystem is added by 
> CONFIG_APP_MSC_STORAGE_FLASH_FATFS=y

MCUBoot is added by 
> CONFIG_BOOTLOADER_MCUBOOT=y

When either one of these options is enabled 