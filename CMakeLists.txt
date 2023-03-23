cmake_minimum_required(VERSION 3.4)
project("Cap'n Proto Root" CXX)
include(CTest)

##########################################################################
# BMD Stuff

# Configure wih Conan
include("${CMAKE_CURRENT_SOURCE_DIR}/conan-cmake.cmake")

# Debug filenames
set(CMAKE_DEBUG_POSTFIX "_d")
set(CMAKE_RELWITHDEBINFO_POSTFIX "_rd")

# When building with Ninja, or with /MP enabled, there is the potential
# for multiple processes to need to lock the same pdb file.
# The /FS option (which is implicitly enabled by /MP) is widely believed
# to be the solution for this, but even with /FS enabled the problem can
# still randomly occur.
# https://stackoverflow.com/a/58020501/149111 suggests that /Z7 should be
# used; rather than placing the debug info into a .pdb file it embeds it
# into the object files in a similar way to gcc/clang which should reduce
# contention and potentially make the build faster... but at the cost of
# larger object files
foreach(flag_var CMAKE_C_FLAGS_DEBUG CMAKE_CXX_FLAGS_DEBUG)
    if (${flag_var} MATCHES "/Zi")
        string(REGEX REPLACE "/Zi" "/Z7" ${flag_var} "${${flag_var}}")
    endif()
endforeach()

# fancy AUTO logic doesn't work as OPENSSL_FOUND is not set for some reason
set(WITH_OPENSSL ON)
set(WITH_ZLIB ON)

##########################################################################


add_subdirectory(c++)
