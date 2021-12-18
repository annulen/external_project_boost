project(ExternalProjectBoost)
cmake_minimum_required(VERSION 3.0)

include(ExternalProject)

set(BOOST_PREFIX ${CMAKE_BINARY_DIR}/boost)

ExternalProject_Add(boost
    URL                "https://boostorg.jfrog.io/artifactory/main/release/1.78.0/source/boost_1_78_0.7z"
    URL_HASH           SHA1=d0a8eebf466fe7f845bfc22c281dc1a692663b2d
    PREFIX             ${BOOST_PREFIX}
    BUILD_IN_SOURCE    TRUE
    CONFIGURE_COMMAND  ./bootstrap.sh --with-libraries=filesystem --with-libraries=system --with-libraries=date_time --with-libraries=program_options --with-libraries=thread --prefix=<INSTALL_DIR>
    BUILD_COMMAND      ./b2 install link=static variant=release threading=multi runtime-link=static
    INSTALL_COMMAND    ""
)
