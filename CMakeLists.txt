cmake_minimum_required(VERSION 3.0.2)
set(CMP0048 NEW)
project(TangoTest LANGUAGES CXX
        VERSION 1.0)
IF NOT(WIN32)
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

IF(WIN32)
  link_directories($ENV{TANGO_PATH}/lib)
  target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} $ENV{TANGO_PATH}/include)
  if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_link_libraries(${PROJECT_NAME} PUBLIC tangod)
  else()
    target_link_libraries(${PROJECT_NAME} PUBLIC tango)
  endif()
ELSE
  target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${TANGO_PKG_INCLUDE_DIRS})
  target_compile_options(${PROJECT_NAME} PUBLIC -std=c++11)
  target_compile_definitions(${PROJECT_NAME} PUBLIC ${TANGO_PKG_CFLAGS_OTHER})
  link_directories(${TANGO_PKG_LIBRARY_DIRS})
  target_link_libraries(${PROJECT_NAME} PUBLIC ${TANGO_PKG_LIBRARIES})
ENDIF()
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})
