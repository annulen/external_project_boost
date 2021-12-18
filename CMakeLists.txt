project(ExternalProjectBoost)
cmake_minimum_required(VERSION 3.12)

include(ExternalProject)

# Boost_ROOT variable is used both in ExternalProject_Add() call and to limit search path of find_package(Boost)
set(Boost_ROOT ${CMAKE_BINARY_DIR}/boost)

ExternalProject_Add(boost
    URL                "https://boostorg.jfrog.io/artifactory/main/release/1.78.0/source/boost_1_78_0.7z"
    URL_HASH           SHA1=d0a8eebf466fe7f845bfc22c281dc1a692663b2d
    PREFIX             ${Boost_ROOT}
    BUILD_IN_SOURCE    TRUE
    CONFIGURE_COMMAND  ./bootstrap.sh --with-libraries=filesystem --with-libraries=system --with-libraries=date_time --with-libraries=program_options --with-libraries=thread --prefix=<INSTALL_DIR>
    BUILD_COMMAND      ./b2 install link=static variant=release threading=multi runtime-link=static
    INSTALL_COMMAND    ""
)

# Boost libraries built with runtime-link=static can only be found with Boost_USE_STATIC_RUNTIME
# See boost/lib/cmake/Boost-1.78.0/BoostConfig.cmake for more details
set(Boost_USE_STATIC_RUNTIME ON)
find_package(Boost COMPONENTS system filesystem program_options thread date_time REQUIRED)
