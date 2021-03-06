cmake_minimum_required(VERSION 3.2)
project(CHATOPERA)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -O3 -fpermissive")

# prefer static library
IF(WIN32)
    SET(CMAKE_FIND_LIBRARY_SUFFIXES .lib .a ${CMAKE_FIND_LIBRARY_SUFFIXES})
ELSE(WIN32)
    SET(CMAKE_FIND_LIBRARY_SUFFIXES .a ${CMAKE_FIND_LIBRARY_SUFFIXES})
ENDIF(WIN32)

SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} 
    ${PROJECT_SOURCE_DIR}/cmake/modules)

# Patch build information
## build date
execute_process(
  COMMAND date "+%Y-%m-%d %H:%M:%S"
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_BUILD_DATE
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
add_definitions("-DGIT_BUILD_DATE=\"${GIT_BUILD_DATE}\"")

## Get the current working branch
execute_process(
  COMMAND git rev-parse --abbrev-ref HEAD
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_COMMIT_BRANCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
add_definitions("-DGIT_COMMIT_BRANCH=\"${GIT_COMMIT_BRANCH}\"")

## git commit hash macro
## https://stackoverflow.com/questions/1435953/how-can-i-pass-git-sha1-to-compiler-as-definition-using-cmake
execute_process(
  COMMAND git rev-parse --short HEAD
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_COMMIT_HASH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
add_definitions("-DGIT_COMMIT_HASH=\"${GIT_COMMIT_HASH}\"")

execute_process(
  COMMAND git config user.name
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_USER_NAME
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
add_definitions("-DGIT_USER_NAME=\"${GIT_USER_NAME}\"")

execute_process(
  COMMAND git config user.email
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_USER_EMAIL
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
add_definitions("-DGIT_USER_EMAIL=\"${GIT_USER_EMAIL}\"")

execute_process(
  COMMAND gcc -dumpversion
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GCC_DUMP_VERSION
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
add_definitions("-DGCC_DUMP_VERSION=\"${GCC_DUMP_VERSION}\"")


# Install third party libs
include(${PROJECT_SOURCE_DIR}/cmake/default.cmake)

set(CMAKE_INSTALL_PREFIX ${PROJECT_SOURCE_DIR})

set(ROOT_INSTALL_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include)
set(ROOT_INSTALL_BIN_DIR ${PROJECT_SOURCE_DIR}/bin)
set(ROOT_INSTALL_LIB_DIR ${PROJECT_SOURCE_DIR}/lib)
set(CHATOPERA_UTILS_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/src/utils)

include_directories(${ROOT_INSTALL_INCLUDE_DIR})
include_directories(${CHATOPERA_UTILS_INCLUDE_DIR})

add_subdirectory(src)

# IF DUMP MORE INFO TO CORE FILE
#SET(CMAKE_BUILD_TYPE "Debug") 
#SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
#SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")


