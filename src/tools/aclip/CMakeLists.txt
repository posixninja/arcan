PROJECT( aclip )
cmake_minimum_required(VERSION 2.8.0 FATAL_ERROR)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../../platform/cmake/modules)

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
	add_definitions(-D_DEBUG)
endif()

if (ARCAN_SOURCE_DIR)
	add_subdirectory(${ARCAN_SOURCE_DIR}/shmif ashmif)
else()
	find_package(arcan_shmif REQUIRED)
endif()

add_definitions(
	-Wall
	-D__UNIX
	-DPOSIX_C_SOURCE
	-DGNU_SOURCE
	-Wno-unused-function
	-std=gnu11 # shmif-api requires this
)

include_directories(${ARCAN_SHMIF_INCLUDE_DIR})

SET(LIBRARIES
	pthread
	m
	${ARCAN_SHMIF_LIBRARY}
)

set(SOURCES
	aclip.c
)

add_executable(${PROJECT_NAME} ${SOURCES})
target_link_libraries(${PROJECT_NAME} ${LIBRARIES})
install(TARGETS ${PROJECT_NAME} DESTINATION bin)

if (CMAKE_SYSTEM_NAME MATCHES "(FreeBSD|NetBSD|OpenBSD)")
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/aclip.1 DESTINATION man/man1)
else()
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/aclip.1 DESTINATION share/man/man1)
endif()
