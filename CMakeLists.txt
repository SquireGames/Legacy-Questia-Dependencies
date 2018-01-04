
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

# prevent gtest from changing settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# define project
project(QuestiaEngineDeps)
set(QENG_VERSION_MAJOR "0")
set(QENG_VERSION_MINOR "0")
set(QENG_VERSION_PATCH "0")
set(QENG_VERSION ${QENG_VERSION_MAJOR}.${QENG_VERSION_MINOR}.${QENG_VERSION_PATCH})

# libs
add_subdirectory(googletest)
add_subdirectory(SDL2-2.0.7)

# add symlink where required for installing on linux
if(NOT WIN32)
    MACRO(ADD_SYMLINK targetLib)
        get_property(LIB_BIN_DIR TARGET ${targetLib} PROPERTY BINARY_DIR)
        get_target_property(LIB_FILE_PREFIX ${targetLib} PREFIX)
        get_target_property(LIB_FILE_BASENAME ${targetLib} OUTPUT_NAME)
        get_target_property(LIB_FILE_SUFFIX ${targetLib} SUFFIX)
        if(LIB_FILE_PREFIX MATCHES "-NOTFOUND")
            if(BUILD_SHARED_LIBS)
                set(LIB_FILE_PREFIX ${CMAKE_SHARED_LIBRARY_PREFIX})
            else()
                set(LIB_FILE_PREFIX ${CMAKE_STATIC_LIBRARY_PREFIX})
            endif()
        endif()
        if(LIB_FILE_BASENAME MATCHES "-NOTFOUND")
            set(LIB_FILE_BASENAME ${targetLib})
        endif()
        if(LIB_FILE_SUFFIX MATCHES "-NOTFOUND")
            if(BUILD_SHARED_LIBS)
                set(LIB_FILE_SUFFIX ${CMAKE_SHARED_LIBRARY_SUFFIX})
            else()
                set(LIB_FILE_SUFFIX ${CMAKE_STATIC_LIBRARY_SUFFIX})
            endif()
        endif()
        
        set(LIB_NAME_FULL ${LIB_FILE_PREFIX}${LIB_FILE_BASENAME}${LIB_FILE_SUFFIX})
        string(REGEX REPLACE "-([0-9]+)\\.([0-9]+)" "" LIB_NAME_NOVER "${LIB_NAME_FULL}")
        
        add_custom_command(
            OUTPUT ${LIB_BIN_DIR}/${LIB_NAME_NOVER}
            COMMAND ${CMAKE_COMMAND} -E create_symlink
                $<TARGET_FILE:${targetLib}>
                ${LIB_BIN_DIR}/${LIB_NAME_NOVER}
            DEPENDS ${targetLib}
        )
        add_custom_target(create_symlink_${targetLib} ALL
            DEPENDS ${LIB_BIN_DIR}/${LIB_NAME_NOVER}
        )
    ENDMACRO()

    if(SDL_SHARED)
        ADD_SYMLINK(SDL2)  
    endif()

endif()
