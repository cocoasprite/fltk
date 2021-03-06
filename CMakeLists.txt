#
# Main CMakeLists.txt to build the FLTK project using CMake (www.cmake.org)
# Written by Michael Surette
#
# Copyright 1998-2020 by Bill Spitzak and others.
#
# This library is free software. Distribution and use rights are outlined in
# the file "COPYING" which should have been included with this file.  If this
# file is missing or damaged, see the license at:
#
#     https://www.fltk.org/COPYING.php
#
# Please see the following page on how to report bugs and issues:
#
#     https://www.fltk.org/bugs.php
#

#######################################################################
# set CMake minimum version (must be before `project()`
#######################################################################

# Note: previous (1.3.x) minimum version
# cmake_minimum_required (VERSION 2.6.3)

# Note: minimum CMake version required by GNUInstallDirs
# cmake_minimum_required (VERSION 2.8.4)

# Minimum CMake version required by FLTK 1.4 (06/2020, work in progress)
cmake_minimum_required (VERSION 3.2.3 FATAL_ERROR)

#######################################################################
# define the FLTK project and version
#######################################################################

project (FLTK VERSION 1.4.0)

#######################################################################
# include macro definitions of generally used macros
#######################################################################

include (CMake/fl_debug_var.cmake)
include (CMake/fl_add_library.cmake)

if (false)
  fl_debug_var (FLTK_VERSION_MAJOR)
  fl_debug_var (FLTK_VERSION_MINOR)
  fl_debug_var (FLTK_VERSION_PATCH)
  fl_debug_var (FLTK_VERSION)
  fl_debug_var (FLTK_VERSION)
endif (false)

#######################################################################
# basic setup
#######################################################################
include (CMake/setup.cmake)

#######################################################################
# check for headers, libraries and functions
#######################################################################
include (CMake/resources.cmake)

#######################################################################
# options
#######################################################################
include (CMake/options.cmake)

#######################################################################
# Android Studio setup
#######################################################################
if (OPTION_CREATE_ANDROID_STUDIO_IDE)
  include (CMake/android.cmake)
endif (OPTION_CREATE_ANDROID_STUDIO_IDE)

#######################################################################
# print (debug) several build variables and options
#######################################################################

set (debug_build 0) # set to 1 to show debug info

if (debug_build)
  message ("")
  message (STATUS "${CMAKE_CURRENT_LIST_DIR}/CMakeLists.txt: set debug_build to 0 to disable the following info:")
  fl_debug_var (WIN32)
  fl_debug_var (MINGW)
  fl_debug_var (CYGWIN)
  fl_debug_var (MSVC)
  fl_debug_var (UNIX)
  fl_debug_var (APPLE)
  fl_debug_var (ANDROID)
  fl_debug_var (CMAKE_BUILD_TYPE)
  fl_debug_var (CMAKE_SIZEOF_VOID_P)
  fl_debug_var (OPTION_OPTIM)
  fl_debug_var (CMAKE_C_FLAGS)
  fl_debug_var (CMAKE_CXX_FLAGS)
  fl_debug_var (CMAKE_EXE_LINKER_FLAGS)
  message (STATUS "${CMAKE_CURRENT_LIST_DIR}/CMakeLists.txt: end of debug_build info.")
endif (debug_build)

unset (debug_build)

#######################################################################
# variables shared by export and install
# export.cmake creates configuration files for direct use in a built but uninstalled FLTK
# install.cmake creates these files for an installed FLTK
# these two would only differ in paths, so common variables are set here
#######################################################################
include (CMake/variables.cmake)

#######################################################################
# final config and export
#######################################################################
include (CMake/export.cmake)

configure_file (
  ${CMAKE_CURRENT_SOURCE_DIR}/abi-version.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/FL/abi-version.h
  @ONLY
)

#######################################################################
# options to build test/demo and example programs
#######################################################################

if (FLTK_BUILD_TEST)
  add_subdirectory (test)
endif (FLTK_BUILD_TEST)

if (FLTK_BUILD_EXAMPLES)
  add_subdirectory (examples)
endif (FLTK_BUILD_EXAMPLES)

#######################################################################
# Android Studio wrapup
#######################################################################
if (OPTION_CREATE_ANDROID_STUDIO_IDE)
  CREATE_ANDROID_IDE_WRAPUP()
endif (OPTION_CREATE_ANDROID_STUDIO_IDE)

#######################################################################
# installation
#######################################################################
include (CMake/install.cmake)

#######################################################################
# Generate Library Export Headers          *** EXPERIMENTAL *** WIP ***
#######################################################################

# Enable (1) or disable (0) generation of experimental headers (WIP)
set (GENERATE_EXPORT_HEADERS 0)

if (OPTION_BUILD_SHARED_LIBS AND GENERATE_EXPORT_HEADERS)

  include (GenerateExportHeader)

  generate_export_header (fltk_SHARED
    BASE_NAME fl
    EXPORT_FILE_NAME FL/fltk_export.h
    STATIC_DEFINE FL_STATIC_LIB
  )

  if (NOT MSVC)

    # Visual Studio builds only one shared lib (DLL)

    generate_export_header (fltk_images_SHARED
      BASE_NAME fl_images
      EXPORT_FILE_NAME FL/fltk_images_export.h
      STATIC_DEFINE FL_STATIC_LIB
    )

    generate_export_header (fltk_forms_SHARED
      BASE_NAME fl_forms
      EXPORT_FILE_NAME FL/fltk_forms_export.h
      STATIC_DEFINE FL_STATIC_LIB
    )

    generate_export_header (fltk_gl_SHARED
      BASE_NAME fl_gl
      EXPORT_FILE_NAME FL/fltk_gl_export.h
      STATIC_DEFINE FL_STATIC_LIB
    )

  endif (NOT MSVC)

endif (OPTION_BUILD_SHARED_LIBS AND GENERATE_EXPORT_HEADERS)

#######################################################################
# Output Configuration Summary
#######################################################################

include (FeatureSummary)

message ("")
feature_summary (WHAT ALL DESCRIPTION "Configuration Summary --\n")

message (STATUS "Static libraries will be written to ${CMAKE_CURRENT_BINARY_DIR}/lib")

if (OPTION_BUILD_SHARED_LIBS)
  message (STATUS "Shared libraries will be written to ${CMAKE_CURRENT_BINARY_DIR}/lib")
else ()
  message (STATUS "Shared libraries will not be built (set OPTION_BUILD_SHARED_LIBS=ON to build)")
endif ()

if (FLTK_BUILD_TEST)
  message (STATUS "Test programs    will be written to ${CMAKE_CURRENT_BINARY_DIR}/bin/test")
endif ()

if (FLTK_BUILD_EXAMPLES)
  message (STATUS "Example programs will be written to ${CMAKE_CURRENT_BINARY_DIR}/bin/examples")
else ()
  message (STATUS "Example programs will not be built (set FLTK_BUILD_EXAMPLES=ON to build)")
endif ()

message ("")
message (STATUS "End of Configuration Summary --\n")
