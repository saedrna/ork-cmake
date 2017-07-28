project(Ork CXX C)
cmake_minimum_required(VERSION 2.6)
cmake_policy(VERSION 2.6)

SET(ORK_VERSION_MAJOR 3)
SET(ORK_VERSION_MINOR 2)
SET(ORK_VERSION "${ORK_VERSION_MAJOR}.${ORK_VERSION_MINOR}")

# Avoid source tree pollution
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)
	message(FATAL_ERROR "In-source builds are not permitted. Make a separate folder for building:\nmkdir build; cd build; cmake ..\nBefore that, remove the files already created:\nrm -rf CMakeCache.txt CMakeFiles")
endif(CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)

# Add a sensible build type default and warning because empty means no optimization and no debug info.
if(NOT CMAKE_BUILD_TYPE)
	message("WARNING: CMAKE_BUILD_TYPE is not defined!\n         Defaulting to CMAKE_BUILD_TYPE=RelWithDebInfo. Use ccmake to set a proper value.")
	set(CMAKE_BUILD_TYPE RelWithDebInfo CACHE STRING "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif(NOT CMAKE_BUILD_TYPE)

# Common deps
find_package(glew REQUIRED) # GLEW::GLEW 
find_package(glfw3 REQUIRED) # glfw
find_package(glut REQUIRED) #GLUT::GLUT
find_package(OpenGL REQUIRED) # OpenGL:GL OpenGL:GLU
set(ORK_3RDPARTY_LIBRARIES OpenGL::GL OpenGL::GLU GLEW::GLEW glfw GLUT::GLUT)

option(BUILD_SHARED       "Build shared library instead of static"   OFF)
option(BUILD_EXAMPLES     "Build examples"                           ON )
option(BUILD_TESTS        "Build tests"                              ON )
option(USE_SHARED_PTR     "Use std::shared_ptr"			    ON )
option(USE_FREEGLUT       "Use freeglut"				    ON )

set(CMAKE_DEBUG_POSTFIX "-gd")

include_directories(${CMAKE_CURRENT_LIST_DIR}/ork)
# Sub dirs
add_subdirectory(libraries)
add_subdirectory(ork)

if(BUILD_EXAMPLES)
	add_subdirectory(examples)
endif(BUILD_EXAMPLES)

if(BUILD_TESTS)
	add_subdirectory(test)
endif(BUILD_TESTS)

# Layout. This works for all platforms:
#   * <prefix>/lib/cmake/<PROJECT-NAME>
#   * <prefix>/lib/
#   * <prefix>/include/
set(config_install_dir "cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

# Configuration
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

# Include module with fuction 'write_basic_package_version_file'
include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion VERSION ${ORK_VERSION}
)

configure_package_config_file(
    "Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(EXPORT ork-targets 
	    NAMESPACE ork::
		DESTINATION cmake/${PROJECT_NAME})

