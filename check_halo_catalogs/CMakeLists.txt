file(GLOB SRC_FILES src/*)

add_executable(make_hist ${SRC_FILES})
target_include_directories(make_hist PRIVATE src)
target_link_libraries(make_hist PRIVATE genericio::genericio_mpi CosmoTools)