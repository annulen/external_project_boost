project(ExternalProjectBoost)
cmake_minimum_required(VERSION 3.12)

include(ExternalProject)

# Boost_ROOT variable is passed to boost and myproject
set(Boost_ROOT ${CMAKE_CURRENT_BINARY_DIR}/boost)

ExternalProject_Add(boost
    URL                "https://boostorg.jfrog.io/artifactory/main/release/1.78.0/source/boost_1_78_0.7z"
    URL_HASH           SHA1=d0a8eebf466fe7f845bfc22c281dc1a692663b2d
    PREFIX             ${Boost_ROOT}
    BUILD_IN_SOURCE    TRUE
    CONFIGURE_COMMAND  ./bootstrap.sh --with-libraries=filesystem --with-libraries=system --with-libraries=date_time --with-libraries=program_options --with-libraries=thread --prefix=<INSTALL_DIR>
    BUILD_COMMAND      ./b2 install link=static variant=release threading=multi runtime-link=static
    INSTALL_COMMAND    ""
    BYPRODUCTS         "${Boost_ROOT}/include" "${Boost_ROOT}/lib/libboost_filesystem.a"
)

add_library(Boost_filesystem INTERFACE)
set_target_properties(Boost_filesystem PROPERTIES
    INTERFACE_LINK_LIBRARIES "${Boost_ROOT}/lib/libboost_filesystem.a"
    INTERFACE_INCLUDE_DIRECTORIES "${Boost_ROOT}/include"
)

add_executable(tut1 tut1.cpp)
target_link_libraries(tut1 PRIVATE Boost_filesystem)
add_dependencies(tut1 boost)
