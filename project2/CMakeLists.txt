project(ExternalProjectBoost)
cmake_minimum_required(VERSION 3.12)

# This function is used to force build of external project at cmake configuration phase
# Source: https://stackoverflow.com/a/23570741
#
function(BuildExternalProject target) # following arguments are passed to ExternalProject_Add
    set(trigger_build_dir ${CMAKE_BINARY_DIR}/force_${target})

    #mktemp dir in build tree
    file(MAKE_DIRECTORY ${trigger_build_dir} ${trigger_build_dir}/build)

    # generate CMake project for dependency
    string(REPLACE ";" " " joined_argn "${ARGN}")
    file(WRITE ${trigger_build_dir}/CMakeLists.txt "
        project(force_${target})
        cmake_minimum_required(VERSION 3.12)
        include(ExternalProject)
        ExternalProject_add(${target} "
            ${joined_argn}
       ")"
    )

    execute_process(COMMAND ${CMAKE_COMMAND} -G ${CMAKE_GENERATOR} ..
        WORKING_DIRECTORY ${trigger_build_dir}/build
    )
    execute_process(COMMAND ${CMAKE_COMMAND} --build .
        WORKING_DIRECTORY ${trigger_build_dir}/build
    )
endfunction()


# Boost_ROOT variable is used both in ExternalProject_Add() call and to limit search path of find_package(Boost)
set(Boost_ROOT ${CMAKE_CURRENT_BINARY_DIR}/boost)

BuildExternalProject(boost
    URL                \"https://boostorg.jfrog.io/artifactory/main/release/1.78.0/source/boost_1_78_0.7z\"
    URL_HASH           SHA1=d0a8eebf466fe7f845bfc22c281dc1a692663b2d
    PREFIX             ${Boost_ROOT}
    BUILD_IN_SOURCE    TRUE
    CONFIGURE_COMMAND  ./bootstrap.sh --with-libraries=filesystem --with-libraries=system --with-libraries=date_time --with-libraries=program_options --with-libraries=thread --prefix=<INSTALL_DIR>
    BUILD_COMMAND      ./b2 install link=static variant=release threading=multi runtime-link=static
    INSTALL_COMMAND    \"\"
)

# Boost libraries built with runtime-link=static can only be found with Boost_USE_STATIC_RUNTIME
# See boost/lib/cmake/Boost-1.78.0/BoostConfig.cmake for more details
set(Boost_USE_STATIC_RUNTIME ON)
find_package(Boost COMPONENTS system filesystem program_options thread date_time REQUIRED)

add_executable(tut1 tut1.cpp)
target_link_libraries(tut1 PRIVATE Boost::filesystem)
