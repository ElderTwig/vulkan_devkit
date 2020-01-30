cmake_minimum_required(VERSION 3.10.2)
project( External )

include( ExternalProject )

ExternalProject_Add( Vulkan-Headers
        GIT_REPOSITORY https://github.com/KhronosGroup/Vulkan-Headers.git
        GIT_TAG origin/master
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-Headers
        BINARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-Headers-build
        CMAKE_ARGS
            -j8
)

ExternalProject_Add( Vulkan-Loader
        GIT_REPOSITORY https://github.com/KhronosGroup/Vulkan-Loader.git
        GIT_TAG origin/master
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-Loader
        BINARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-Loader-build
        DEPENDS Vulkan-Headers
        CMAKE_ARGS
            -j8
            -DVULKAN_HEADERS_INSTALL_DIR=${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-Headers-build/install
)

ExternalProject_Add( glslang
        GIT_REPOSITORY https://github.com/KhronosGroup/glslang.git
        GIT_TAG origin/master
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/glslang
        BINARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/glslang-build
        CMAKE_ARGS
            -j8
            -DCMAKE_INSTALL_PREFIX=install
)

include( FindPythonInterp )
if( NOT PYTHONINTERP_FOUND )
    message( FATAL_ERROR "Python interpreter not found" )
elseif( PYTHON_VERSION_MAJOR LESS 3 )
    message( FATAL_ERROR "Need python version of 3.0 or higher" )
endif()

ExternalProject_Add_Step( glslang spirvTools
        COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/glslang/update_glslang_sources.py
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/glslang
        DEPENDEES update
        DEPENDERS configure
)

ExternalProject_Add( Vulkan-ValidationLayers
        GIT_REPOSITORY https://github.com/KhronosGroup/Vulkan-ValidationLayers.git
        GIT_TAG origin/master
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-ValidationLayers
        BINARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-ValidationLayers-build
        DEPENDS Vulkan-Headers Vulkan-Loader glslang
        CMAKE_ARGS
            -j8
            -DVULKAN_HEADERS_INSTALL_DIR=${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-Headers-build/install
            -DVULKAN_LOADER_INSTALL_DIR=${CMAKE_CURRENT_SOURCE_DIR}/Vulkan-Loader-build/install
            -DGLSLANG_INSTALL_DIR=${CMAKE_CURRENT_SOURCE_DIR}/glslang-build/install
)

ExternalProject_Add( GLFW
        GIT_REPOSITORY https://github.com/glfw/glfw.git
        GIT_TAG origin/master
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/GLFW
        BINARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/GLFW-build
        CMAKE_ARGS
            -j8
            -DCMAKE_INSTALL_PREFIX=install
)

ExternalProject_Add( GLM
        GIT_REPOSITORY https://github.com/g-truc/glm.git
        GIT_TAG origin/master
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/GLM
        BINARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/GLM-build
        INSTALL_COMMAND ""
        CMAKE_ARGS
            -j8
)