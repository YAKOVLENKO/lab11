cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)

include(cmake/HunterGate.cmake)

HunterGate(
    URL "https://github.com/YAKOVLENKO/hunter/archive/v0.19.137.1.tar.gz"
    SHA1 "3ebd749c5c1d8319feb8e3fb1c86aa6a53f99239"
)

project(demo)
hunter_add_package(print)
hunter_add_package(Boost COMPONENTS program_options)
find_package(print)
find_package(Boost CONFIG REQUIRED program_options)

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/sources/demo.cpp)
target_link_libraries(demo print Boost::program_options)

install(TARGETS demo RUNTIME DESTINATION bin)
