cmake_minimum_required(VERSION 3.5)

# Force cmake to use the NEW policy regaringthe "VERSION" management.
cmake_policy(SET CMP0048 NEW)

project(SoftRobots VERSION 1.0)

option(USE_COMPRESSED_ROW_SPARSE "Use compressed row sparse format in the mappings" ON)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}")


set(HEADER_FILES
    component/initSoftRobots.h
        )

set(SOURCE_FILES
    component/initSoftRobots.cpp
        )

file(GLOB_RECURSE RESOURCE_FILES  "*.md" "*.psl" "*.py" "*.pyscn" "*.scn")

include(cmake/environment.cmake)
include(cmake/utils.cmake)

include(component/behavior/Behavior_Files.cmake)
include(component/constraint/Constraint_Files.cmake)
include(component/controller/Controller_Files.cmake)
include(component/engine/Engine_Files.cmake)
include(component/forcefield/Forcefield_Files.cmake)

add_library(${PROJECT_NAME} SHARED ${HEADER_FILES} ${SOURCE_FILES} ${DOC_FILES} ${RESOURCE_FILES})

include(component/behavior/Behavior_Links.cmake)
include(component/constraint/Constraint_Links.cmake)
include(component/controller/Controller_Links.cmake)
include(component/engine/Engine_Links.cmake)
include(component/forcefield/Forcefield_Links.cmake)

set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${HEADER_FILES}")

target_include_directories(${PROJECT_NAME} PUBLIC "$<INSTALL_INTERFACE:include>")

find_package(SofaFramework REQUIRED)

set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-DSOFA_BUILD_SOFTROBOTS")
target_link_libraries(${PROJECT_NAME} SofaCore SofaConstraint SofaBaseMechanics SofaUserInteraction)

# Config files and install rules for pythons scripts
sofa_set_python_directory(${PROJECT_NAME} "python")

## Install rules for the library and headers; CMake package configurations files
sofa_create_package(SoftRobots ${PROJECT_VERSION} ${PROJECT_NAME} SoftRobots)

if(SOFA_BUILD_TESTS)
    add_subdirectory(tests)
endif()


