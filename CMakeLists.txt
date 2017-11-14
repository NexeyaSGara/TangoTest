cmake_minimum_required(VERSION 3.0.2)
set(CMP0048 NEW)

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        project(TangoTest_d LANGUAGES CXX
                VERSION 1.0)
else()
        project(TangoTest LANGUAGES CXX
                VERSION 1.0)
endif()
#need to define the version of the library
set(MAJOR_VERSION "9")
set(MINOR_VERSION "2")
set(PATCH_VERSION "6")
#convenient versions
set(LIBRARY_VERSION "${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}")
IF(NOT WIN32)
include(FindPkgConfig)

pkg_search_module(TANGO_PKG REQUIRED tango)
ENDIF()

IF (WIN32)
add_definitions(-D_WIN32)
add_definitions(-DWIN32)
add_definitions(-D_CONSOLE)
IF(CMAKE_CL_64)
add_definitions(-D_64BITS)
IF (MSVC14)
add_definitions(-D_TIMERS_T_)
add_definitions(-DHAVE_STRUCT_TIMESPEC)
add_definitions(-D_TIMESPEC_DEFINED)
add_definitions(-DSTRUCT_TIMESPEC_HAS_TV_SEC)
add_definitions(-DSTRUCT_TIMESPEC_HAS_TV_NSEC)
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
  add_executable(${PROJECT_NAME}_dyn ${SOURCES} ${HEADERS})
  
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
add_definitions(-D_DEBUG)
install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        CONFIGURATIONS Debug)
install(FILES Debug/${PROJECT_NAME}_d.exe DESTINATION bin)
install(TARGETS ${PROJECT_NAME}_dyn
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        CONFIGURATIONS Debug)
install(FILES Debug/${PROJECT_NAME}_d_dyn.exe DESTINATION bin)
else()
add_definitions(-DNDEBUG)
install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin)
install(TARGETS ${PROJECT_NAME}_dyn
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin)
endif()
  set_property(TARGET ${PROJECT_NAME} PROPERTY LINK_FLAGS "/force:multiple")
  target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
  set_property(TARGET ${PROJECT_NAME} PROPERTY COMPILE_DEFINITIONS "_WINSTATIC;")
  set_property(TARGET ${PROJECT_NAME}_dyn PROPERTY COMPILE_DEFINITIONS "LOG4TANGO_HAS_DLL;TANGO_HAS_DLL;")
  if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libtangod.lib
                                                $ENV{TANGO_PATH}/lib/omniORB4d.lib
                                                $ENV{TANGO_PATH}/lib/omniDynamic4d.lib
                                                $ENV{TANGO_PATH}/lib/COS4d.lib
                                                $ENV{TANGO_PATH}/lib/omnithreadd.lib
                                                $ENV{TANGO_PATH}/bin/msvcstubd.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/tangod.lib
                                                $ENV{TANGO_PATH}/bin/omniORB4_rtd.lib
                                                $ENV{TANGO_PATH}/bin/omniDynamic4_rtd.lib
                                                $ENV{TANGO_PATH}/lib/COS4_rtd.lib
                                                $ENV{TANGO_PATH}/bin/omnithread_rtd.lib
                                                $ENV{TANGO_PATH}/bin/msvcstubd.lib)
    target_link_libraries(${PROJECT_NAME} PUBLIC ws2_32 mswsock advapi32 comctl32 odbc32)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/pthreadVC2.lib)
  IF(MSVC90)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v90-mt-s-gd-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v90-mt-gd-4_0_5.lib)
  ELSEIF(MSVC10)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v100-mt-s-gd-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v100-mt-gd-4_0_5.lib)
  ELSEIF(MSVC12)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v120-mt-s-gd-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v120-mt-gd-4_0_5.lib)
  ELSEIF(MSVC14)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v140-mt-s-gd-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v140-mt-gd-4_0_5.lib)
  ENDIF()
  else()
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libtango.lib
                                                $ENV{TANGO_PATH}/lib/omniORB4.lib
                                                $ENV{TANGO_PATH}/lib/omniDynamic4.lib
                                                $ENV{TANGO_PATH}/lib/COS4.lib
                                                $ENV{TANGO_PATH}/lib/omnithread.lib
                                                $ENV{TANGO_PATH}/bin/msvcstub.lib)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/tango.lib
                                                $ENV{TANGO_PATH}/bin/omniORB4_rt.lib
                                                $ENV{TANGO_PATH}/bin/omniDynamic4_rt.lib
                                                $ENV{TANGO_PATH}/bin/COS4_rt.lib
                                                $ENV{TANGO_PATH}/bin/omnithread_rt.lib
                                                $ENV{TANGO_PATH}/bin/msvcstub.lib)
    target_link_libraries(${PROJECT_NAME} PUBLIC ws2_32 mswsock advapi32 comctl32 odbc32)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/pthreadVC2.lib)
  IF(MSVC90)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v90-mt-s-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v90-mt-4_0_5.lib)
  ELSEIF(MSVC10)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v100-mt-s-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v100-mt-4_0_5.lib)
  ELSEIF(MSVC12)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v120-mt-s-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v120-mt-4_0_5.lib)
  ELSEIF(MSVC14)
    target_link_libraries(${PROJECT_NAME} PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v140-mt-s-4_0_5.lib)
    target_link_libraries(${PROJECT_NAME}_dyn PUBLIC $ENV{TANGO_PATH}/lib/libzmq-v140-mt-4_0_5.lib)
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
IF(MSVC90)
      if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        IF(CMAKE_CL_64)
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_v90_x64_debug)
        ELSE()
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_v90_x86_debug)
        ENDIF()
      else()
        IF(CMAKE_CL_64)
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_v90_x64)
        ELSE()
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_v90_x86)
        ENDIF()
      endif(CMAKE_BUILD_TYPE STREQUAL "Debug")
    ELSE()
      IF(CMAKE_CL_64)
        if (CMAKE_BUILD_TYPE STREQUAL "Debug")  
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_${CMAKE_VS_PLATFORM_TOOLSET}_x64_debug)
        else()
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_${CMAKE_VS_PLATFORM_TOOLSET}_x64)
        endif()
      ELSE()
        if (CMAKE_BUILD_TYPE STREQUAL "Debug")
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_${CMAKE_VS_PLATFORM_TOOLSET}_x86_debug)
        else()
          set(CPACK_PACKAGE_FILE_NAME TangoTest_${LIBRARY_VERSION}_${CMAKE_VS_PLATFORM_TOOLSET}_x86)
        endif()
      ENDIF()
    ENDIF(MSVC90)
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
