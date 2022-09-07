file(GLOB SRC_FILES src/*)

add_executable(match_halos ${SRC_FILES})
target_include_directories(match_halos PRIVATE src)
target_link_libraries(match_halos PRIVATE genericio::genericio_mpi CosmoTools)