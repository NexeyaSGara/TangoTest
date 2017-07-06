cmake_minimum_required(VERSION 3.0.2)
set(CMP0048 NEW)

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        project(TangoTest_d LANGUAGES CXX
                VERSION 1.0)
else()
        project(TangoTest LANGUAGES CXX
                VERSION 1.0)
endif()
IF(NOT WIN32)
include(FindPkgConfig)

pkg_search_module(TANGO_PKG REQUIRED tango)
ENDIF()

set(SOURCES
        ClassFactory.cpp
        TangoTest.cpp
        TangoTestClass.cpp
        TangoTestStateMachine.cpp
        main.cpp)

set(HEADERS
        TangoTest.h
        TangoTestClass.h)

add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

IF(WIN32)
  target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
  set(${PROJECT_NAME} INCLUDE_DIRS "$ENV{TANGO_PATH}/include")
  target_include_directories(${PROJECT_NAME} PUBLIC "$ENV{TANGO_PATH}/include")
  include_directories(${PROJECT_NAME} PUBLIC "$ENV{TANGO_PATH}/include")
  set(${PROJECT_NAME} INCLUDE_DIRS "$ENV{TANGO_PATH}/include/include")
  target_include_directories(${PROJECT_NAME} PUBLIC "$ENV{TANGO_PATH}/include/include")
  include_directories(${PROJECT_NAME} PUBLIC "$ENV{TANGO_PATH}/include/include")
  link_directories("$ENV{TANGO_PATH}/lib")
  link_directories("$ENV{TANGO_PATH}/bin")
  if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/tangod.lib
                                                $ENV{TANGO_PATH}/lib/omniORB4d.lib
                                                $ENV{TANGO_PATH}/bin/omniORB4_rtd.lib
                                                $ENV{TANGO_PATH}/bin/omniDynamic4_rtd.lib
                                                $ENV{TANGO_PATH}/bin/omnithread_rtd.lib
                                                $ENV{TANGO_PATH}/bin/COS4_rtd.lib
                                                $ENV{TANGO_PATH}/bin/msvcstubd.lib)
  IF(MSVC90)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v90-mt-gd-4_1_7.lib)
  ELSEIF(MSVC10)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v100-mt-gd-4_1_7.lib)
  ELSEIF(MSVC12)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v120-mt-gd-4_1_7.lib)
  ELSEIF(MSVC14)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v1400-mt-gd-4_1_7.lib)
  ENDIF()
  else()
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/tango.lib 
                                                $ENV{TANGO_PATH}/lib/omniORB4.lib
                                                $ENV{TANGO_PATH}/bin/omniORB4_rt.lib
                                                $ENV{TANGO_PATH}/bin/omniDynamic4_rt.lib
                                                $ENV{TANGO_PATH}/bin/omnithread_rt.lib
                                                $ENV{TANGO_PATH}/bin/COS4_rt.lib)
    target_link_libraries(${PROJECT_NAME} PUBLIC ws2_32 mswsock advapi32 comctl32 odbc32)
  IF(MSVC90)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v90-mt-4_1_7.lib)
  ELSEIF(MSVC10)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v100-mt-4_1_7.lib)
  ELSEIF(MSVC12)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v120-mt-4_1_7.lib)
  ELSEIF(MSVC14)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v1400-mt-4_1_7.lib)
  ENDIF()
  endif()
ELSE()
  target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${TANGO_PKG_INCLUDE_DIRS})
  target_compile_options(${PROJECT_NAME} PUBLIC -std=c++11)
  target_compile_definitions(${PROJECT_NAME} PUBLIC ${TANGO_PKG_CFLAGS_OTHER})
  link_directories(${TANGO_PKG_LIBRARY_DIRS})
  target_link_libraries(${PROJECT_NAME} PUBLIC ${TANGO_PKG_LIBRARIES})
ENDIF()
include(CPack)
