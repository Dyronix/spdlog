#
# Copyright(c) 2015 Ruslan Baratov.
# Distributed under the MIT License (http://opensource.org/licenses/MIT)
#
# --------------------------
# Setup sources
# --------------------------
message(CHECK_START "Generating ${SPDLOG_LIBRARY_NAME}")

#---------------------------------------------------------------------------------------
# Compiler config
#---------------------------------------------------------------------------------------
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
SET(EXPORT_TARGETS ${SPDLOG_INTERFACE_NAME})
SET(EXPORT_TARGETS_NAME ${SPDLOG_LIBRARY_NAME}_targets_name)

INSTAll_TARGETS(${EXPORT_TARGETS} ${EXPORT_TARGETS_NAME})
INSTALL_HEADERS(${SPDLOG_INCLUDE_DIRECTORY}/${SPDLOG_LIBRARY_NAME} ${CMAKE_INSTALL_INCLUDEDIR}/${SPDLOG_LIBRARY_NAME})

#---------------------------------------------------------------------------------------
# Custom target to integrate into the IDE
#---------------------------------------------------------------------------------------
file(GLOB_RECURSE TARGET_INC "${SPDLOG_INCLUDE_DIRECTORY}/*.h")

add_custom_target(${SPDLOG_LIBRARY_NAME} SOURCES ${TARGET_INC})

set_target_properties(${SPDLOG_LIBRARY_NAME} PROPERTIES   VS_DEBUGGER_WORKING_DIRECTORY    "${OUTPUT_BINDIR}"              # Working directory
                                                           FOLDER                           "0. External Libraries")        # Project filter location

message(CHECK_PASS "Done")