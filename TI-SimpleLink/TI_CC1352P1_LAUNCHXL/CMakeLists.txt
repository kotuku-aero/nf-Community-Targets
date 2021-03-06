#
# Copyright (c) .NET Foundation and Contributors
# See LICENSE file in the project root for full license information.
#

include(binutils.common)
include(binutils.TI_SimpleLink)

ENABLE_LANGUAGE(ASM)

# add packages
NF_ADD_COMMON_PACKAGES()
NF_ADD_PLATFORM_PACKAGES()

#######################################

add_subdirectory("common")
add_subdirectory("nanoCLR")

#######################################

NF_ADD_PLATFORM_SYSCONFIG_STEPS(CC1352 CC13X2)

#######################
# nanoCLR executable

add_executable(
    # executables for project, project sources
    ${NANOCLR_PROJECT_NAME}.elf

    ${CMAKE_CURRENT_SOURCE_DIR}/CC1352P1_LAUNCHXL_fxns.c
)

NF_ADD_PLATFORM_DEPENDENCIES(${NANOCLR_PROJECT_NAME})

NF_ADD_COMMON_SOURCES(${NANOCLR_PROJECT_NAME})
NF_ADD_PLATFORM_SOURCES(${NANOCLR_PROJECT_NAME})

# include directories for nanoCLR
NF_ADD_COMMON_INCLUDE_DIRECTORIES(${NANOCLR_PROJECT_NAME})
NF_ADD_PLATFORM_INCLUDE_DIRECTORIES(${NANOCLR_PROJECT_NAME})

# set compiler options
nf_set_compiler_options(${NANOCLR_PROJECT_NAME}.elf)

# set compiler definitions
nf_set_compiler_definitions(${NANOCLR_PROJECT_NAME}.elf -DDeviceFamily_CC13X2 )

# set compiler definitions pertaining PA availability
nf_set_compiler_definitions(${NANOCLR_PROJECT_NAME}.elf -DCCFG_FORCE_VDDR_HH=0x1 )

# set linker files
if(CMAKE_BUILD_TYPE MATCHES Debug OR CMAKE_BUILD_TYPE MATCHES RelWithDebInfo)
    nf_set_linker_file(${NANOCLR_PROJECT_NAME}.elf ${CMAKE_CURRENT_SOURCE_DIR}/nanoCLR/CC13x2_26x2_CLR-DEBUG.ld)
else()
    nf_set_linker_file(${NANOCLR_PROJECT_NAME}.elf ${CMAKE_CURRENT_SOURCE_DIR}/nanoCLR/CC13x2_26x2_CLR.ld)
endif()

# set linker options
nf_set_linker_options(${NANOCLR_PROJECT_NAME}.elf -Wl,-T,${CMAKE_CURRENT_BINARY_DIR}/configPkg/linker.cmd)

# add other linker flags
###########################################################
# the size of CRT heap stacks is defined here
# set_property(TARGET ${NANOBOOTER_PROJECT_NAME}.elf APPEND_STRING PROPERTY LINK_FLAGS ",--defsym=__crt_heap_size__=0x0")
set_property(TARGET ${NANOCLR_PROJECT_NAME}.elf APPEND_STRING PROPERTY LINK_FLAGS ",--defsym=__crt_heap_size__=0x0")

# generate output files
# nf_generate_build_output_files(${NANOBOOTER_PROJECT_NAME}.elf)
nf_generate_build_output_files(${NANOCLR_PROJECT_NAME}.elf)
