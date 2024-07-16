# Top-level CMakeLists.txt for the skeleton application.
#
# Copyright (c) 2017 Open Source Foundries Limited
# Copyright (c) 2018 Foundries.io Ltd
#
# SPDX-License-Identifier: Apache-2.0
#
# This provides a basic application structure suitable for loading by
# mcuboot, which is easy to customize on a per-board basis. It can be
# used as a starting point for new applications.

cmake_minimum_required(VERSION 3.20.0)

# find_package(Zephyr) in order to load application boilerplate:
# https://docs.zephyrproject.org/latest/develop/application/index.html
find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})
project(MCUBOOT_EXFAT)

# This string ends up getting printed in the device console
if (NOT DEFINED FROM_WHO)
  set(FROM_WHO Zephyr)
endif()

target_compile_definitions(app PRIVATE "-DMCUBOOT_HELLO_WORLD_FROM=\"${FROM_WHO}\"")

target_sources_ifdef(
  CONFIG_REAL_MAIN
  app PRIVATE 
  src/real_main.c
)

if(NOT CONFIG_REAL_MAIN)
  message("NOT REAL MAIN")
  target_sources(
    app PRIVATE 
    src/stub_main.c
  )
endif()

if(CONFIG_BUILD_WITH_TFM)
  target_include_directories(app PRIVATE
    $<TARGET_PROPERTY:tfm,TFM_BINARY_DIR>/install/interface/include
  )
endif()