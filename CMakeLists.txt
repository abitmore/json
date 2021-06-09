#
# Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
#
# Distributed under the Boost Software License, Version 1.0. (See accompanying
# file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
#
# Official repository: https://github.com/boostorg/json
#

cmake_minimum_required(VERSION 3.5...3.16)

set(BOOST_JSON_VERSION 2)
if(BOOST_SUPERPROJECT_VERSION)
    set(BOOST_JSON_VERSION ${BOOST_SUPERPROJECT_VERSION})
endif()

project(boost_json VERSION "${BOOST_JSON_VERSION}" LANGUAGES CXX)

file(GLOB_RECURSE BOOST_JSON_HEADERS CONFIGURE_DEPENDS
  include/boost/*.hpp
  include/boost/*.ipp
  include/boost/*.natvis
)

set(BOOST_JSON_SOURCES
  ${CMAKE_CURRENT_SOURCE_DIR}/src/src.cpp
)

add_library(boost_json ${BOOST_JSON_HEADERS} ${BOOST_JSON_SOURCES})
add_library(Boost::json ALIAS boost_json)

target_link_libraries(boost_json
  PUBLIC
    Boost::align
    Boost::assert
    Boost::config
    Boost::container
    Boost::exception
    Boost::mp11
    Boost::system
    Boost::throw_exception
    Boost::utility
)

target_compile_features(boost_json PUBLIC cxx_constexpr)

target_include_directories(boost_json PUBLIC include)

target_compile_definitions(boost_json PUBLIC BOOST_JSON_NO_LIB=1)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(boost_json PUBLIC BOOST_JSON_DYN_LINK=1)
else()
    target_compile_definitions(boost_json PUBLIC BOOST_JSON_STATIC_LINK=1)
endif()

if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)

function(add_boost_dependency name)
  add_subdirectory("../${name}" "libs/${name}")
endfunction()

# Primary

add_boost_dependency(align)
add_boost_dependency(assert)
add_boost_dependency(config)
add_boost_dependency(container)
add_boost_dependency(exception)
add_boost_dependency(mp11)
add_boost_dependency(system)
add_boost_dependency(throw_exception)
add_boost_dependency(utility)

# Secondary

add_boost_dependency(core)
add_boost_dependency(static_assert)
add_boost_dependency(intrusive)
add_boost_dependency(move)
add_boost_dependency(type_traits)
add_boost_dependency(winapi)
add_boost_dependency(smart_ptr)
add_boost_dependency(tuple)
add_boost_dependency(container_hash)
add_boost_dependency(io)
add_boost_dependency(preprocessor)
add_boost_dependency(detail)
add_boost_dependency(integer)
add_boost_dependency(predef)

#

option(BOOST_JSON_BUILD_TESTS "Build boost::json tests" ON)
option(BOOST_JSON_BUILD_FUZZERS "Build boost::json fuzzers" ON)
option(BOOST_JSON_BUILD_EXAMPLES "Build boost::json examples" ON)
option(BOOST_JSON_BUILD_BENCHMARKS "Build boost::json benchmarks" OFF)

if(BOOST_JSON_BUILD_TESTS)
    include(CTest)
    if(BUILD_TESTING)
        add_subdirectory(test)
    endif()
endif()

if(BOOST_JSON_BUILD_FUZZERS)
    add_subdirectory(fuzzing)
endif()

if(BOOST_JSON_BUILD_EXAMPLES)
    add_subdirectory(example)
endif()

if(BOOST_JSON_BUILD_BENCHMARKS)
    add_subdirectory(bench)
endif()

endif()
