project(MyProject)
cmake_minimum_required(VERSION 3.12)

# If Boost_ROOT variable is set when cmake is called, find_package limits its search to that directory
find_package(Boost COMPONENTS system filesystem program_options thread date_time REQUIRED)

add_executable(tut1 tut1.cpp)
target_link_libraries(tut1 PRIVATE Boost::filesystem)
