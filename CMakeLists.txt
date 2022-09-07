cmake_minimum_required(VERSION 3.11)
project(HACC_check LANGUAGES C CXX)


## DEPENDENCIES ##
# MPI (if found: MPI_FOUND)
find_package(MPI REQUIRED)

# OpenMP  (if found: OpenMP_FOUND)
find_package(OpenMP REQUIRED)

# CosmoTools
# requires specifying HACC_SRC and HACC_BUILD
if(NOT DEFINED ENV{HACC_SRC})
    message(FATAL_ERROR "HACC_SRC env variable not specified")
endif()
if(NOT DEFINED ENV{HACC_BUILD})
    message(FATAL_ERROR "HACC_BUILD env variable not specified")
endif()
# assert cosmotools is compiled
set(COSMOTOOLS_INCLUDEPATH $ENV{HACC_SRC}/cosmotools)
set(COSMOTOOLS_LIBPATH $ENV{HACC_BUILD}/mpi/lib)
if(NOT EXISTS ${COSMOTOOLS_LIBPATH}/libCosmoTools.a)
    message(FATAL_ERROR "HACC: cosmotools library is not compiled")
endif()

add_library(CosmoTools STATIC IMPORTED)
set_property(TARGET CosmoTools PROPERTY IMPORTED_LOCATION ${COSMOTOOLS_LIBPATH}/libCosmoTools.a)
target_include_directories(CosmoTools INTERFACE ${COSMOTOOLS_INCLUDEPATH}/algorithms/halofinder)
target_include_directories(CosmoTools INTERFACE ${COSMOTOOLS_INCLUDEPATH}/algorithms/mergertree_ct)
target_include_directories(CosmoTools INTERFACE ${COSMOTOOLS_INCLUDEPATH}/common)

# GenericIO
include(FetchContent)
FetchContent_Declare(
    genericio
    GIT_REPOSITORY https://git.cels.anl.gov/hacc/genericio.git
    GIT_TAG master
    GIT_SHALLOW YES
    GIT_PROGRESS TRUE
    USES_TERMINAL_DOWNLOAD TRUE   # <---- this is needed only for Ninja
)

FetchContent_GetProperties(genericio)
if(NOT genericio_POPULATED)
    set(FETCHCONTENT_QUIET OFF)
    FetchContent_Populate(genericio)
    add_subdirectory(${genericio_SOURCE_DIR} ${genericio_BINARY_DIR})
endif()


# Targets
# output everything to the build folder instead of subdirectories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
add_subdirectory(check_halo_direct)
add_subdirectory(check_halo_catalogs)
add_subdirectory(check_lightcone)