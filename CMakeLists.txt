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

IF (WIN32)
add_definitions(-D_WIN32)
add_definitions(-DWIN32)
add_definitions(-D__OSVERSION__=4)
add_definitions(-D__POSIX_NT__)
add_definitions(-D__x86__)
add_definitions(-D_CRT_SECURE_NO_WARNINGS)
add_definitions(-DOMNI_UNLOADABLE_STUBS)
add_definitions(-DTANGO_HAS_DLL)
add_definitions(-DLOG4TANGO_HAS_DLL)
add_definitions(-DHAVE_STRUCT_TIMESPEC)
add_definitions(-D_TIMESPEC_DEFINED)
add_definitions(-D_MSVC_VER)
add_definitions(-DSTRUCT_TIMESPEC_HAS_TV_SEC)
add_definitions(-DSTRUCT_TIMESPEC_HAS_TV_NSEC)
IF(CMAKE_CL_64)
add_definitions(-D_64BITS)
IF (MSVC14)
add_definitions(-D_TIMERS_T_)
ENDIF()
ELSE()
add_definitions(-DJPG_USE_ASM)
ENDIF()
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

  set(${PROJECT_NAME} INCLUDE_DIRS "$ENV{TANGO_PATH}/include")
  #target_include_directories(${PROJECT_NAME} PUBLIC "$ENV{TANGO_PATH}/include")
  include_directories(${PROJECT_NAME} PUBLIC "$ENV{TANGO_PATH}/include")
  link_directories("$ENV{TANGO_PATH}/bin")
  link_directories("$ENV{TANGO_PATH}/lib")
  message("$ENV{TANGO_PATH}/include")
  
  add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})
  
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
add_definitions(-D_DEBUG)
install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        CONFIGURATIONS Debug)
else()
install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin)
endif()
  set_property(TARGET ${PROJECT_NAME} PROPERTY LINK_FLAGS "/force:multiple")
  target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
  if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/tangod.lib
                                                $ENV{TANGO_PATH}/bin/omniORB421_rtd.lib
                                                $ENV{TANGO_PATH}/bin/omniDynamic421_rtd.lib
                                                $ENV{TANGO_PATH}/bin/COS421_rtd.lib
                                                $ENV{TANGO_PATH}/bin/omnithread40_rtd.lib)
    target_link_libraries(${PROJECT_NAME} PUBLIC ws2_32 mswsock advapi32 comctl32 odbc32)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/pthreadVC2.lib)
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
                                                $ENV{TANGO_PATH}/bin/omniORB421_rt.lib
                                                $ENV{TANGO_PATH}/bin/omniDynamic421_rt.lib
                                                $ENV{TANGO_PATH}/bin/COS421_rt.lib
                                                $ENV{TANGO_PATH}/bin/omnithread40_rt.lib)
    target_link_libraries(${PROJECT_NAME} PUBLIC ws2_32 mswsock advapi32 comctl32 odbc32)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/pthreadVC2.lib)
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
  add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})
  target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${TANGO_PKG_INCLUDE_DIRS})
  target_compile_options(${PROJECT_NAME} PUBLIC -std=c++11)
  target_compile_definitions(${PROJECT_NAME} PUBLIC ${TANGO_PKG_CFLAGS_OTHER})
  link_directories(${TANGO_PKG_LIBRARY_DIRS})
  target_link_libraries(${PROJECT_NAME} PUBLIC ${TANGO_PKG_LIBRARIES})
ENDIF()
IF(WIN32)

  set (CPACK_PACKAGE_VENDOR "www.tango-controls.org")
  set (CPACK_PACKAGE_DESCRIPTION_SUMMARY "Tango - Connecting Things Together")
  set (CPACK_PACKAGE_VERSION "${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}")
  set (CPACK_PACKAGE_VERSION_MAJOR ${MAJOR_VERSION})
  set (CPACK_PACKAGE_VERSION_MINOR ${MINOR_VERSION})
  set (CPACK_PACKAGE_VERSION_PATCH ${PATCH_VERSION})
  
  set(CPACK_NSIS_HELP_LINK "http://www.tango-controls.org")
  set(CPACK_NSIS_URL_INFO_ABOUT "http://www.tango-controls.org")
  set(CPACK_NSIS_MODIFY_PATH ON)
  set(CPACK_NSIS_ENABLE_UNINSTALL_BEFORE_INSTALL ON)
  set(CPACK_NSIS_MENU_LINKS
      "http://tango-controls.readthedocs.io/en/latest/" "Tango Doc")
ENDIF()
include(CPack)
