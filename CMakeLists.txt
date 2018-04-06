cmake_minimum_required(VERSION 2.8)

project(openhmd C CXX)
set(CMAKE_C_FLAGS "-std=c99") # -fsanitize=address")
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

set(LIB_VERSION_MAJOR 0)
set(LIB_VERSION_MINOR 1)
set(LIB_VERSION_PATCH 0)
set(LIB_VERSION_STRING ${LIB_VERSION_MAJOR}.${LIB_VERSION_MINOR}.${LIB_VERSION_PATCH})

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")
include_directories(${CMAKE_CURRENT_LIST_DIR}/include)

if (MSVC)
  # Add the "lib" prefix for generated .lib outputs.
  set(LIB_PREFIX lib)
else (MSVC)
  # When building with "make", "lib" prefix will be added automatically by
  # the build tool.
  set(LIB_PREFIX)
endif (MSVC)

#source files set just for Android
set(openhmd_source_files
	${CMAKE_CURRENT_LIST_DIR}/src/openhmd.c
	${CMAKE_CURRENT_LIST_DIR}/src/platform-win32.c
	${CMAKE_CURRENT_LIST_DIR}/src/drv_dummy/dummy.c
	${CMAKE_CURRENT_LIST_DIR}/src/omath.c
	${CMAKE_CURRENT_LIST_DIR}/src/platform-posix.c
	${CMAKE_CURRENT_LIST_DIR}/src/fusion.c
	${CMAKE_CURRENT_LIST_DIR}/src/shaders.c
)

OPTION(OPENHMD_DRIVER_OCULUS_RIFT "Oculus Rift DK1 and DK2" ON)
OPTION(OPENHMD_DRIVER_DEEPOON "Deepoon E2" ON)
OPTION(OPENHMD_DRIVER_WMR "Windows Mixed Reality" ON)
OPTION(OPENHMD_DRIVER_PSVR "Sony PSVR" ON)
OPTION(OPENHMD_DRIVER_HTC_VIVE "HTC Vive" ON)
OPTION(OPENHMD_DRIVER_NOLO "NOLO VR CV1" ON)
OPTION(OPENHMD_DRIVER_EXTERNAL "External sensor driver" ON)
OPTION(OPENHMD_DRIVER_ANDROID "General Android driver" OFF)

OPTION(OPENHMD_EXAMPLE_SIMPLE "Simple test binary" ON)
OPTION(OPENHMD_EXAMPLE_SDL "SDL OpenGL test (outdated)" OFF)

if(OPENHMD_DRIVER_OCULUS_RIFT)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_oculus_rift/rift.c
	${CMAKE_CURRENT_LIST_DIR}/src/drv_oculus_rift/packet.c
	)
	add_definitions(-DDRIVER_OCULUS_RIFT)

	find_package(HIDAPI REQUIRED)
	include_directories(${HIDAPI_INCLUDE_DIRS})
	set(LIBS ${LIBS} ${HIDAPI_LIBRARIES})
endif(OPENHMD_DRIVER_OCULUS_RIFT)

if(OPENHMD_DRIVER_DEEPOON)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_deepoon/deepoon.c
	${CMAKE_CURRENT_LIST_DIR}/src/drv_deepoon/packet.c
	)
	add_definitions(-DDRIVER_DEEPOON)

	find_package(HIDAPI REQUIRED)
	include_directories(${HIDAPI_INCLUDE_DIRS})
	set(LIBS ${LIBS} ${HIDAPI_LIBRARIES})
endif(OPENHMD_DRIVER_DEEPOON)

if(OPENHMD_DRIVER_WMR)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_wmr/wmr.c
	${CMAKE_CURRENT_LIST_DIR}/src/drv_wmr/packet.c
	)
	add_definitions(-DDRIVER_WMR)

	find_package(HIDAPI REQUIRED)
	include_directories(${HIDAPI_INCLUDE_DIRS})
	set(LIBS ${LIBS} ${HIDAPI_LIBRARIES})
endif(OPENHMD_DRIVER_WMR)

if(OPENHMD_DRIVER_PSVR)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_psvr/psvr.c
	${CMAKE_CURRENT_LIST_DIR}/src/drv_psvr/packet.c
	)
	add_definitions(-DDRIVER_PSVR)

	find_package(HIDAPI REQUIRED)
	include_directories(${HIDAPI_INCLUDE_DIRS})
	set(LIBS ${LIBS} ${HIDAPI_LIBRARIES})
endif(OPENHMD_DRIVER_PSVR)

if(OPENHMD_DRIVER_HTC_VIVE)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_htc_vive/vive.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/mjson.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_usb.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_imu.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_process.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/ootx_decoder.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_driverman.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_vive.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_config.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_cal.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_disambiguator.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_turveybiguator.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser_epnp.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/epnp/epnp.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser_sba.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/sba/sba_lapack.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/sba/sba_chkjac.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/sba/sba_crsm.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/sba/sba_levmar.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/sba/sba_levmar_wrap.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_sensor_activations.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/minimal_opencv.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser_turveytori.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser_octavioradii.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser_charlesslow.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser_daveortho.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/poser_dummy.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_reproject.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_default_devices.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/src/survive_playback.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/json_helpers.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/linmath.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/jsmn.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/crc32.c
	${CMAKE_CURRENT_LIST_DIR}/src/ext_deps/libsurvive/redist/puff.c
	)
	add_definitions(-DDRIVER_HTC_VIVE)
	add_definitions(-DUSE_DOUBLE -DFLT=double)

	find_package(HIDAPI REQUIRED)
	include_directories(${HIDAPI_INCLUDE_DIRS}
		${CMAKE_CURRENT_SOURCE_DIR}/src/ext_deps/libsurvive/include/libsurvive
		${CMAKE_CURRENT_SOURCE_DIR}/src/ext_deps/libsurvive/redist
		${CMAKE_CURRENT_SOURCE_DIR}/src/ext_deps/libsurvive)
	set(LIBS ${LIBS} ${HIDAPI_LIBRARIES} -L${CMAKE_CURRENT_SOURCE_DIR}/src/ext_deps/libsurvive/lib/ z opencv_core lapacke)
endif(OPENHMD_DRIVER_HTC_VIVE)

if(OPENHMD_DRIVER_NOLO)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_nolo/nolo.c
	${CMAKE_CURRENT_LIST_DIR}/src/drv_nolo/packet.c
	)
	add_definitions(-DDRIVER_NOLO)

	find_package(HIDAPI REQUIRED)
	include_directories(${HIDAPI_INCLUDE_DIRS})
	set(LIBS ${LIBS} ${HIDAPI_LIBRARIES})
endif(OPENHMD_DRIVER_NOLO)

if (OPENHMD_DRIVER_EXTERNAL)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_external/external.c
	)
	add_definitions(-DDRIVER_EXTERNAL)
endif(OPENHMD_DRIVER_EXTERNAL)

if (OPENHMD_DRIVER_ANDROID)
	set(openhmd_source_files ${openhmd_source_files}
	${CMAKE_CURRENT_LIST_DIR}/src/drv_android/android.c
	)
	add_definitions(-DDRIVER_ANDROID)
endif(OPENHMD_DRIVER_ANDROID)

if (OPENHMD_EXAMPLE_SIMPLE)
	add_subdirectory(./examples/simple)
endif(OPENHMD_EXAMPLE_SIMPLE)

if (OPENHMD_EXAMPLE_SDL)
	find_package(SDL2 REQUIRED)
	find_package(GLEW REQUIRED)
	find_package(OpenGL REQUIRED)
	add_subdirectory(./examples/opengl)
endif (OPENHMD_EXAMPLE_SDL)

if (UNIX)
	set(LIBS ${LIBS} rt pthread)
endif (UNIX)

link_libraries(${LIBS})
add_library(openhmd-shared SHARED ${openhmd_source_files})
SET_TARGET_PROPERTIES(openhmd-shared PROPERTIES OUTPUT_NAME openhmd CLEAN_DIRECT_OUTPUT 1 VERSION ${LIB_VERSION_STRING} SOVERSION ${LIB_VERSION_MAJOR})
add_library(openhmd-static STATIC ${openhmd_source_files})
SET_TARGET_PROPERTIES(openhmd-static PROPERTIES OUTPUT_NAME openhmd CLEAN_DIRECT_OUTPUT 1)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_DIR}/)

set_property(TARGET openhmd-static PROPERTY C_STANDARD 11)
set_property(TARGET openhmd-shared PROPERTY C_STANDARD 11)

#install properties
install (TARGETS openhmd-shared openhmd-static DESTINATION lib)
install (FILES include/openhmd.h DESTINATION include)
