cmake_minimum_required(VERSION 3.0)

PROJECT(yasm)

# Setup options when not already specified.
IF(NOT BUILD_STATIC)
	OPTION(BUILD_STATIC "Build static libraries" OFF)
ENDIF(NOT BUILD_STATIC)
IF(NOT BUILD_SHARED_LIBS)
	OPTION(BUILD_SHARED_LIBS "Build shared libraries" ON)
ENDIF(NOT BUILD_SHARED_LIBS)
IF(NOT ENABLE_NLS)
	OPTION(ENABLE_NLS "Enable message translations" OFF)
ENDIF(NOT ENABLE_NLS)
IF(NOT YASM_BUILD_TESTS)
	OPTION(YASM_BUILD_TESTS "Enable building of tests" ON)
ENDIF(NOT YASM_BUILD_TESTS)

# Default build type to debug if not set
IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel." FORCE)
ENDIF(NOT CMAKE_BUILD_TYPE)

# Where to look first for cmake modules
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/modules")

INCLUDE(YasmMacros)
INCLUDE(VersionGen)

IF(YASM_BUILD_TESTS)
    ENABLE_TESTING()
ENDIF(YASM_BUILD_TESTS)

VERSION_GEN(PACKAGE_VERSION "${PROJECT_BINARY_DIR}/YASM-VERSION-FILE" "1.3.0")

set (PACKAGE_STRING "yasm ${PACKAGE_VERSION}")

INCLUDE_DIRECTORIES(AFTER ${PROJECT_BINARY_DIR} ${PROJECT_SOURCE_DIR})

INCLUDE(ConfigureChecks.cmake)

ADD_SUBDIRECTORY(tools)
ADD_SUBDIRECTORY(libyasm)
ADD_SUBDIRECTORY(modules)
ADD_SUBDIRECTORY(frontends)

target_include_directories(libyasm PUBLIC ${PROJECT_SOURCE_DIR})
target_include_directories(libyasm PUBLIC ${PROJECT_BINARY_DIR})
