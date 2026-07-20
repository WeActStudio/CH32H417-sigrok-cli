##
## This file is part of the sigrok-cli project.
##
## Copyright (C) 2013 Bert Vermeulen <bert@biot.com>
##
## This program is free software: you can redistribute it and/or modify
## it under the terms of the GNU General Public License as published by
## the Free Software Foundation, either version 3 of the License, or
## (at your option) any later version.
##
## This program is distributed in the hope that it will be useful,
## but WITHOUT ANY WARRANTY; without even the implied warranty of
## MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
## GNU General Public License for more details.
##
## You should have received a copy of the GNU General Public License
## along with this program.  If not, see <http://www.gnu.org/licenses/>.
##

cmake_minimum_required(VERSION 3.10)

project(sigrok-cli C)

include(GNUInstallDirs)

#===============================================================================
#= User Options
#-------------------------------------------------------------------------------

option(DISABLE_WERROR "Build without -Werror" TRUE)
option(ENABLE_DECODE "Build with libsigrokdecode" TRUE)

if(WIN32)
	# Windows does not support UNIX signals.
	# Keep compat with original sigrok-cli autoconf build.
	set(ENABLE_DECODE TRUE)
endif()

if(NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE Release CACHE STRING
	"Choose the type of build (None, Debug, Release, RelWithDebInfo, MinSizeRel)."
	FORCE)
endif()

#===============================================================================
#= Dependencies
#-------------------------------------------------------------------------------

find_package(PkgConfig REQUIRED)

# Mandatory dependencies
pkg_check_modules(GLIB REQUIRED glib-2.0>=2.32.0)
pkg_check_modules(LIBSIGROK REQUIRED libsigrok>=0.5.0)

if(ENABLE_DECODE)
	pkg_check_modules(LIBSIGROKDECODE REQUIRED libsigrokdecode>=0.5.0)
endif()

#===============================================================================
#= Sources
#-------------------------------------------------------------------------------

set(SIGROK_CLI_SOURCES
	main.c
	show.c
	device.c
	session.c
	input.c
	output.c
	decode.c
	parsers.c
	anykey.c
	options.c
)

#===============================================================================
#= Defines
#-------------------------------------------------------------------------------

# Version info
set(PACKAGE_VERSION "0.8.0")
set(SC_PACKAGE_VERSION_MAJOR 0)
set(SC_PACKAGE_VERSION_MINOR 8)
set(SC_PACKAGE_VERSION_MICRO 0)
set(SC_PACKAGE_VERSION_STRING "0.8.0-git")

# Check for srd_session_send_eof
# This function exists in our libsigrokdecode, so always define it when decode is enabled
if(ENABLE_DECODE)
	set(HAVE_SRD_SESSION_SEND_EOF 1)
endif()

# Generate config.h
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake
	${CMAKE_CURRENT_BINARY_DIR}/config.h)

#===============================================================================
#= Compiler flags
#-------------------------------------------------------------------------------

if(NOT DISABLE_WERROR)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Werror")
endif()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -std=c99")

#===============================================================================
#= Include paths
#-------------------------------------------------------------------------------

include_directories(
	${CMAKE_CURRENT_BINARY_DIR}  # For config.h
	${CMAKE_CURRENT_SOURCE_DIR}  # For sigrok-cli.h
	${GLIB_INCLUDE_DIRS}
	${LIBSIGROK_INCLUDE_DIRS}
)

if(ENABLE_DECODE)
	include_directories(${LIBSIGROKDECODE_INCLUDE_DIRS})
endif()

#===============================================================================
#= Link directories
#-------------------------------------------------------------------------------

link_directories(
	${GLIB_LIBRARY_DIRS}
	${LIBSIGROK_LIBRARY_DIRS}
)

if(ENABLE_DECODE)
	link_directories(${LIBSIGROKDECODE_LIBRARY_DIRS})
endif()

#===============================================================================
#= Executable
#-------------------------------------------------------------------------------

add_executable(sigrok-cli ${SIGROK_CLI_SOURCES})

# Link libraries
target_link_libraries(sigrok-cli
	${GLIB_LIBRARIES}
	${LIBSIGROK_LIBRARIES}
)

if(ENABLE_DECODE)
	target_link_libraries(sigrok-cli ${LIBSIGROKDECODE_LIBRARIES})
endif()

# Platform-specific libs
if(WIN32)
	target_link_libraries(sigrok-cli ws2_32 setupapi ole32)
else()
	target_link_libraries(sigrok-cli m pthread dl)
endif()

# Add defines
target_compile_definitions(sigrok-cli PRIVATE
	HAVE_CONFIG_H
	HAVE_SRD=$<BOOL:${ENABLE_DECODE}>
)

#===============================================================================
#= Install
#-------------------------------------------------------------------------------

install(TARGETS sigrok-cli DESTINATION bin)

#===============================================================================
#= Summary
#-------------------------------------------------------------------------------

message(STATUS "")
message(STATUS "sigrok-cli configuration:")
message(STATUS "  Build type:        ${CMAKE_BUILD_TYPE}")
message(STATUS "  Enable decode:     ${ENABLE_DECODE}")
message(STATUS "  Install prefix:    ${CMAKE_INSTALL_PREFIX}")
message(STATUS "")