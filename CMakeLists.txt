
cmake_minimum_required (VERSION 2.8.10)
enable_language(C CXX)

##cxx specification
set(CMAKE_CXX_STANDARD 14)

##define directory structure of output 
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")

##define project properties
project(QuestiaEngineDeps)
set(QENG_VERSION_MAJOR "0")
set(QENG_VERSION_MINOR "0")
set(QENG_VERSION_PATCH "0")
set(QENG_VERSION_TWEAK "0")
set(QENG_VERSION ${QENG_VERSION_MAJOR}.${QENG_VERSION_MINOR}.${QENG_VERSION_PATCH}.${QENG_VERSION_TWEAK})

##needs to be called from top directory
enable_testing()

##libs
add_subdirectory(googletest)
add_subdirectory(SDL2-2.0.7)
