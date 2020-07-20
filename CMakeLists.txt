#
# Copyright(c) 2015 Ruslan Baratov.
# Distributed under the MIT License (http://opensource.org/licenses/MIT)
#

# --------------------------
# Setup sources
# --------------------------
SET (SPDLOG_LIBRARY_NAME spdlog)
SET (SPDLOG_INTERFACE_NAME ${SPDLOG_LIBRARY_NAME}_interface)

message(STATUS "Generating ${SPDLOG_LIBRARY_NAME}")

cmake_minimum_required(VERSION 3.1)

project(${SPDLOG_LIBRARY_NAME} VERSION 0.17.0 LANGUAGES CXX)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

#---------------------------------------------------------------------------------------
# compiler config
#---------------------------------------------------------------------------------------
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU" OR "${CMAKE_CXX_COMPILER_ID}" MATCHES "Clang")
    set(CMAKE_CXX_FLAGS "-Wall -O3 ${CMAKE_CXX_FLAGS}")
endif()

#---------------------------------------------------------------------------------------
# spdlog target
#---------------------------------------------------------------------------------------
add_library(${SPDLOG_INTERFACE_NAME} INTERFACE)

target_include_directories( ${SPDLOG_INTERFACE_NAME} INTERFACE
											"$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include>"
    										"$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>")

#---------------------------------------------------------------------------------------
# Install/export targets and files
#---------------------------------------------------------------------------------------
# set files and directories
set(header_base "${CMAKE_CURRENT_SOURCE_DIR}/include")
set(config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
set(include_install_dir "${CMAKE_INSTALL_INCLUDEDIR}")
set(pkgconfig_install_dir "${CMAKE_INSTALL_LIBDIR}/pkgconfig")
set(version_config "${CMAKE_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${PROJECT_NAME}Config.cmake")
set(pkg_config "${CMAKE_BINARY_DIR}/${PROJECT_NAME}.pc")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

# generate package version file
write_basic_package_version_file("${version_config}" COMPATIBILITY SameMajorVersion)

# configure pkg config file
configure_file("cmake/spdlog.pc.in" "${pkg_config}" @ONLY)

# install targets
install(
    TARGETS ${SPDLOG_INTERFACE_NAME}
    EXPORT "${targets_export_name}"
)

# install headers
install(
    DIRECTORY "${header_base}/${PROJECT_NAME}"
    DESTINATION "${include_install_dir}"
)

# install project version file
install(
    FILES "${version_config}"
    DESTINATION "${config_install_dir}"
)

# install pkg config file
install(
    FILES "${pkg_config}"
    DESTINATION "${pkgconfig_install_dir}"
)

# install project config file
install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
    FILE ${project_config}
)

# export build directory config file
export(
    EXPORT ${targets_export_name}
    NAMESPACE "${namespace}"
    FILE ${project_config}
)

# register project in CMake user registry
export(PACKAGE ${PROJECT_NAME})

file(GLOB_RECURSE TARGET_INC "${header_base}/*.h")

add_custom_target(${SPDLOG_LIBRARY_NAME} SOURCES ${TARGET_INC})

set_target_properties( ${SPDLOG_LIBRARY_NAME} PROPERTIES   VS_DEBUGGER_WORKING_DIRECTORY    "${OUTPUT_BINDIR}"              # Working directory
                                                           FOLDER                           "0. External Libraries")        # Project filter location
