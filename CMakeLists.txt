cmake_minimum_required(VERSION 3.5)
# set(ANDROID_PLATFORM "android-23")
# set(ANDROID_ABI "arm64-v8a")
# set(CMAKE_TOOLCHAIN_FILE "$ENV{ANDROID_NDK}/build/cmake/android.toolchain.cmake")

# or cmake -DCMAKE_INSTALL_PREFIX=< install_path > ..
SET(CMAKE_INSTALL_PREFIX dist)
project(udt)
set(CMAKE_BUILD_TYPE "Release")
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++17")


file(GLOB SOURCE_FILES "src/*.cc" "src/*.cpp" "src/*.h")
# add_executable(${PROJECT_NAME} ${SOURCE_FILES})
ADD_LIBRARY(${PROJECT_NAME} STATIC ${SOURCE_FILES})

# target_link_libraries(${PROJECT_NAME} PRIVATE stdc++fs)

include_directories("app" "src")
# only strip executable
# set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -s")
# set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -s")

ADD_EXECUTABLE( "appclient" "app/appclient.cpp" ) 
TARGET_LINK_LIBRARIES( "appclient" ${PROJECT_NAME} Threads::Threads)

ADD_EXECUTABLE( "appserver" "app/appserver.cpp" ) 
TARGET_LINK_LIBRARIES( "appserver" ${PROJECT_NAME} Threads::Threads)

ADD_EXECUTABLE( "recvfile" "app/recvfile.cpp" ) 
TARGET_LINK_LIBRARIES( "recvfile" ${PROJECT_NAME} Threads::Threads)

ADD_EXECUTABLE( "sendfile" "app/sendfile.cpp" ) 
TARGET_LINK_LIBRARIES( "sendfile" ${PROJECT_NAME} Threads::Threads)

ADD_EXECUTABLE( "test" "app/test.cpp" ) 
TARGET_LINK_LIBRARIES( "test" ${PROJECT_NAME} Threads::Threads)

set_target_properties(
    appclient appserver recvfile sendfile test
    PROPERTIES LINK_FLAGS_RELEASE -s)

if(WIN32)
  target_link_libraries(appclient wsock32 ws2_32)
  target_link_libraries(appserver wsock32 ws2_32)
  target_link_libraries(recvfile wsock32 ws2_32)
  target_link_libraries(sendfile wsock32 ws2_32)
  target_link_libraries(test wsock32 ws2_32)
endif()
# last slash(/) is important
install(
    DIRECTORY
    "src/"
    DESTINATION
    "include"
    FILES_MATCHING
    PATTERN
    "*.h"
)
install(TARGETS ${PROJECT_NAME} DESTINATION lib)