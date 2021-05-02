cmake_minimum_required(VERSION 3.10)
project(bluevk)

set(TARGET              bluevk)
set(PUBLIC_HDR_DIR      include)

file(GLOB_RECURSE PUBLIC_HDRS ${PUBLIC_HDR_DIR}/${TARGET}/*.h)

set(SRCS src/BlueVK.cpp)

# OS specific sources
if (WIN32)
    set(SRCS ${SRCS} src/BlueVKWindows.cpp)
elseif (APPLE)
    set(SRCS ${SRCS} src/BlueVKDarwin.cpp)
elseif (LINUX OR ANDROID)
    set(SRCS ${SRCS} src/BlueVKLinuxAndroid.cpp)
else()
    message(FATAL_ERROR "Platform not supported. BlueVK supports Linux, Android, MacOS X, and Windows.")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility=hidden")

include_directories(${PUBLIC_HDR_DIR})

add_library(${TARGET} STATIC ${PUBLIC_HDRS} ${SRCS})

# add target properties
set_target_properties(${TARGET} PROPERTIES FOLDER "libs")

target_link_libraries(${TARGET} utils math)

target_include_directories(${TARGET} PUBLIC ${PUBLIC_HDR_DIR})

# test_bluevk is not supported on mobile or Windows
if (NOT ANDROID AND NOT IOS AND NOT WIN32)
    add_executable(test_bluevk tests/test_bluevk_sdl.cpp)
    set_target_properties(test_bluevk PROPERTIES FOLDER "libs/tests")
    target_link_libraries(test_bluevk PRIVATE dl bluevk sdl2)
endif()

install(TARGETS ${TARGET} ARCHIVE DESTINATION lib/${DIST_DIR})
