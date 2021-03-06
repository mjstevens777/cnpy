CMAKE_MINIMUM_REQUIRED(VERSION 2.6 FATAL_ERROR)
if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

project(CNPY)

include(FindPkgConfig)
PKG_CHECK_MODULES(LIBZIP REQUIRED libzip)

include_directories(${LIBZIP_INCLUDE_DIRS})
link_directories(${LIBZIP_LIBRARY_DIRS})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

option(ENABLE_STATIC "Build static (.a) library" ON)
option(ENABLE_BINARIES "Install binaries, e.g. mat2npz" ON)

add_library(cnpy SHARED "cnpy.cpp")
target_link_libraries(cnpy ${LIBZIP_LIBRARIES})
install(TARGETS "cnpy" LIBRARY DESTINATION lib PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

if(ENABLE_STATIC)
    add_library(cnpy-static STATIC "cnpy.cpp")
    set_target_properties(cnpy-static PROPERTIES OUTPUT_NAME "cnpy")
    install(TARGETS "cnpy-static" ARCHIVE DESTINATION lib)
endif(ENABLE_STATIC)

install(FILES "cnpy.h" DESTINATION include)

if (ENABLE_BINARIES)
    install(FILES "mat2npz" "npy2mat" "npz2mat" DESTINATION bin PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
endif(ENABLE_BINARIES)

add_executable(example1 example1.cpp)
target_link_libraries(example1 cnpy)
