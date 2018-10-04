cmake_minimum_required(VERSION 3.5)
project(plog VERSION 1.0.0 LANGUAGES CXX)

#Make sure that custom modules like FindRapidJSON are found
list(INSERT CMAKE_MODULE_PATH 0 ${CMAKE_SOURCE_DIR}/cmake)

##############################################
# Declare dependencies

##############################################
# Create target and set properties

add_library(plog
    INTERFACE
)

#Add an alias so that library can be used inside the build tree, e.g. when testing
add_library(Plog::plog ALIAS plog)

#Set target properties
target_include_directories(plog
    INTERFACE
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

target_compile_features(plog INTERFACE cxx_auto_type)
target_compile_options(plog INTERFACE $<$<CXX_COMPILER_ID:GNU>:-Wall>)

##############################################
# Installation instructions

include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/Plog)

install(TARGETS plog
    EXPORT plog-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

#This is required so that the exported target has the name Plog and not plog
set_target_properties(plog PROPERTIES EXPORT_NAME Plog)

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

#Export the targets to a script
install(EXPORT plog-targets
  FILE
    PlogTargets.cmake
  NAMESPACE
    Plog::
  DESTINATION
    ${INSTALL_CONFIGDIR}
)

#Create a ConfigVersion.cmake file
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/PlogConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/PlogConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/PlogConfig.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

#Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/PlogConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/PlogConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

##############################################
## Exporting from the build tree

export(EXPORT plog-targets FILE ${CMAKE_CURRENT_BINARY_DIR}/PlogTargets.cmake NAMESPACE Plog::)

#Register package in user's package registry
export(PACKAGE Plog)

