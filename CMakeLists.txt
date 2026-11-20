cmake_minimum_required(VERSION 3.16)

project(PiExchange LANGUAGES CXX)

# C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Select component: exchange or generator
set(COMPONENT "exchange" CACHE STRING "Component to build")
set_property(CACHE COMPONENT PROPERTY STRINGS exchange generator)

if(NOT COMPONENT STREQUAL "exchange" AND
   NOT COMPONENT STREQUAL "generator")
    message(FATAL_ERROR "Invalid component")
endif()

# Default to Debug
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Build type" FORCE)
endif()

# Measurement build optimisation
set(CMAKE_CXX_FLAGS_MEASURE "-O2" CACHE STRING
    "Measurement compiler flags")

# Create executable
add_executable(${COMPONENT}
    src/${COMPONENT}/main.cpp
)

# Compiler warnings for both builds
target_compile_options(${COMPONENT} PRIVATE
    -Wall
    -Wextra
    -Wconversion
    -Werror
)

# Debug configuration
target_compile_options(${COMPONENT} PRIVATE
    $<$<CONFIG:Debug>:-O0>
    $<$<CONFIG:Debug>:-g>
)

# Executable output directory
set_target_properties(${COMPONENT} PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
)
