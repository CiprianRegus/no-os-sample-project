cmake_minimum_required(VERSION 3.27)

find_package (Python3 COMPONENTS Interpreter Development)
find_package(Git)

include(FetchContent)

if (NO_OS_PATH AND EXISTS ${NO_OS_PATH})

        message(STATUS "Using local no-OS from: ${NO_OS_PATH}")
        set(CMAKE_TOOLCHAIN_FILE ${NO_OS_PATH}/drivers/platform/${PLATFORM}/toolchain.cmake)
        add_subdirectory(${NO_OS_PATH} ${CMAKE_BINARY_DIR}/no-os-core-build)

else()

        set(FETCHCONTENT_UPDATES_DISCONNECTED ON)
        FetchContent_Declare(
                no-os-core
                GIT_REPOSITORY https://github.com/analogdevicesinc/no-OS.git
                GIT_TAG dev/cmake
                GIT_SHALLOW TRUE
                GIT_SUBMODULES ""
        )

        set(FETCHCONTENT_QUIET OFF)
        message(STATUS "Cloning No-OS... 📥")
        FetchContent_MakeAvailable(no-os-core)

endif()

project(no-os-sample VERSION 1.0 LANGUAGES C CXX ASM)

add_executable(sample main.c)

config_platform_sdk(sample)
add_flash_target(sample)
post_build_config(sample)

target_link_libraries(sample no-os)