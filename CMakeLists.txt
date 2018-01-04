
cmake_minimum_required (VERSION 2.8.10)
enable_language(C CXX)

# cxx specification
set(CMAKE_CXX_STANDARD 17)

# define directory structure of output 
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")

# ensure shared libs are built correctly on Visual Studio
option(BUILD_SHARED_LIBS OFF)
if(NOT BUILD_SHARED_LIBS AND CMAKE_GENERATOR MATCHES "Visual Studio")
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
endif()

# not allow in-source builds
if(${PROJECT_SOURCE_DIR} STREQUAL ${PROJECT_BINARY_DIR})
	message(FATAL_ERROR "In-source builds not allowed")
endif()

# TODO: remove when gtest removes TR1
add_definitions(-DGTEST_HAS_TR1_TUPLE=0)
add_definitions(-DGTEST_HAS_STD_TUPLE=1)

# define project
project(QuestiaEngineDeps)
set(QENG_VERSION_MAJOR "0")
set(QENG_VERSION_MINOR "0")
set(QENG_VERSION_PATCH "0")
set(QENG_VERSION ${QENG_VERSION_MAJOR}.${QENG_VERSION_MINOR}.${QENG_VERSION_PATCH})

# libs
add_subdirectory(googletest)
add_subdirectory(SDL2-2.0.7)
