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
)

ExternalProject_Add(myproject
    DEPENDS boost
    SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/src"
    BINARY_DIR "${CMAKE_CURRENT_BINARY_DIR}/myproject"
    CMAKE_ARGS
        # Boost_ROOT variable is used by find_package(Boost) in subproject
        "-DBoost_ROOT=${Boost_ROOT}"

        # Boost libraries built with runtime-link=static can only be found with Boost_USE_STATIC_RUNTIME
        # See boost/lib/cmake/Boost-1.78.0/BoostConfig.cmake for more details
        -DBoost_USE_STATIC_RUNTIME=ON
    INSTALL_COMMAND    ""
)

