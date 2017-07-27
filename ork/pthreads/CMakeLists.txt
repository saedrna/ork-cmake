file(GLOB PTHREAD_SRC pthread.c pthread.h)
add_library(pthread SHARED ${PTHREAD_SRC})
INSTALL(TARGETS pthread EXPORT ork-targets 
    LIBRARY DESTINATION lib 
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin)
