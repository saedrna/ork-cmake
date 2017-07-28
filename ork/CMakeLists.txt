set(LIBNAME "ork")
add_definitions(-DPTW32_STATIC_LIB)

# Sources
include_directories("${CMAKE_CURRENT_LIST_DIR}" "${CMAKE_CURRENT_LIST_DIR}/pthreads" "${PROJECT_SOURCE_DIR}" "${PROJECT_SOURCE_DIR}/libraries" "${PROJECT_SOURCE_DIR}/libraries/*")
file(GLOB SOURCE_FILES *.cpp */*.cpp */*.h )

# Create Libs
add_library(ork ${SOURCE_FILES})
target_link_libraries(ork ${ORK_3RDPARTY_LIBRARIES})

if(BUILD_SHARED_LIBS AND MSVC)
	target_compile_definitions(ork INTERFACE "-DORK_API=__declspec(dllimport)"
								   PRIVATE "-DORK_API=__declspec(dllexport)")
else(BUILD_SHARED_LIBS AND MSVC)
	target_compile_definitions(ork PUBLIC -DORK_API=)
endif(BUILD_SHARED_LIBS AND MSVC)

if(USE_SHARED_PTR)
	target_compile_definitions(ork PUBLIC -DUSE_SHARED_PTR)
endif(USE_SHARED_PTR)
if(USE_FREEGLUT)
	target_compile_definitions(ork PUBLIC -DUSEFREEGLUT)
endif(USE_FREEGLUT)
target_compile_definitions(ork PUBLIC -DTIXML_USE_STL)

target_include_directories(ork INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>  # <prefix>/include/mylib
)

# Install headers
FOREACH(subdir core math render resource scenegraph taskgraph ui util)
  FILE(GLOB include-files ${subdir}/*.h)
  INSTALL(FILES ${include-files} DESTINATION include/ork/${subdir})
ENDFOREACH(subdir)

INSTALL(FILES tinyxml/tinyxml.h DESTINATION include/ork/resource/tinyxml)
INSTALL(FILES stbi/stb_image.h DESTINATION include/ork/resource/stbi)

# Install library
INSTALL(TARGETS ork EXPORT ork-targets 
	LIBRARY DESTINATION lib 
	ARCHIVE DESTINATION lib
	RUNTIME DESTINATION bin)
