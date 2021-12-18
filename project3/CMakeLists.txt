project(BoostCMakeProject)
cmake_minimum_required(VERSION 3.12)

# git clone --depth=1 https://github.com/Orphis/boost-cmake.git
# You can also make it git submodule or use FetchContent to download from git
add_subdirectory(boost-cmake)

add_executable(tut1 tut1.cpp)
target_link_libraries(tut1 PRIVATE Boost::filesystem)
