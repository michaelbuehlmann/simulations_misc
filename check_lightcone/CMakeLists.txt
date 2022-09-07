file(GLOB SRC_FILES src/*)

add_executable(match_lightcone ${SRC_FILES})
target_include_directories(match_lightcone PRIVATE src)
target_link_libraries(match_lightcone PRIVATE genericio::genericio_mpi CosmoTools)