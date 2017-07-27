set(LIBNAME "ork")
add_definitions(-DPTW32_STATIC_LIB)

# Sources
include_directories("${CMAKE_CURRENT_LIST_DIR}" "${CMAKE_CURRENT_LIST_DIR}/pthreads" "${PROJECT_SOURCE_DIR}" "${PROJECT_SOURCE_DIR}/libraries" "${PROJECT_SOURCE_DIR}/libraries/*")
file(GLOB SOURCE_FILES *.cpp */*.cpp */*.h pthreads/pthread.c)

# Libraries
set(LIBS OpenGL::GL OpenGL::GLU GLEW::GLEW glfw)

if(BUILD_SHARED_LIBS AND MSVC)
	add_definitions("-DORK_API=__declspec(dllexport)")
else(BUILD_SHARED_LIBS AND MSVC)
	add_definitions(-DORK_API=)
endif(BUILD_SHARED_LIBS AND MSVC)

# Create Libs
add_library(${LIBNAME} ${SOURCE_FILES})
target_link_libraries(${LIBNAME} ${LIBS})

if(BUILD_SHARED_LIBS AND MSVC)
	target_compile_definitions(ork INTERFACE "-DORK_API=__declspec(dllimport)")
else(BUILD_SHARED_LIBS AND MSVC)
	target_compile_definitions(ork INTERFACE -DORK_API=)
endif(BUILD_SHARED_LIBS AND MSVC)

# Install headers
FOREACH(subdir core math render resource scenegraph taskgraph ui util)
  FILE(GLOB include-files ${subdir}/*.h)
  INSTALL(FILES ${include-files} DESTINATION include/ork/${subdir})
ENDFOREACH(subdir)

INSTALL(FILES tinyxml/tinyxml.h DESTINATION include/ork/resource/tinyxml)
INSTALL(FILES stbi/stb_image.h DESTINATION include/ork/resource/stbi)
INSTALL(FILES pthreads/pthread.h DESTINATION include/ork)

# Install library
INSTALL(TARGETS ork EXPORT ork-targets 
	LIBRARY DESTINATION lib 
	ARCHIVE DESTINATION lib
	RUNTIME DESTINATION bin)

message(STATUS "Setting ork cflags for installation package")

if(USE_SHARED_PTR)
	message(STATUS "Setting shared ptr usage to final package")
	set(ORK_CFLAGS ${ORK_CFLAGS} "-DUSE_SHARED_PTR")
endif(USE_SHARED_PTR)
if(USE_FREEGLUT)
	message(STATUS "Setting freeglut usage to final package")
	set(ORK_CFLAGS ${ORK_CFLAGS} "-DUSEFREEGLUT")
endif(USE_FREEGLUT)

message(STATUS "ork cflags: " ${ORK_CFLAGS})

