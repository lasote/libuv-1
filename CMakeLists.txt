# This CMakeLists.txt file helps defining your block building and compiling
# Include the main biicode macros and functions
# To learn more about the CMake use with biicode, visit http://docs.biicode.com/c++/building.html
# Or check the examples below

macro(ADD_OSX_FRAMEWORK fwname target)
    find_library(FRAMEWORK_${fwname}
    NAMES ${fwname}
    PATHS ${CMAKE_OSX_SYSROOT}/System/Library
    PATH_SUFFIXES Frameworks
    NO_DEFAULT_PATH)
    if( ${FRAMEWORK_${fwname}} STREQUAL FRAMEWORK_${fwname}-NOTFOUND)
        MESSAGE(ERROR ": Framework ${fwname} not found")
    else()
        TARGET_LINK_LIBRARIES(${target} PUBLIC "${FRAMEWORK_${fwname}}/${fwname}")
        MESSAGE(STATUS "Framework ${fwname} found at ${FRAMEWORK_${fwname}}")
    endif()
endmacro(ADD_FRAMEWORK)


include(${CMAKE_HOME_DIRECTORY}/biicode.cmake)
# Initializes block variables
INIT_BIICODE_BLOCK()
IF(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
   SET(LINUX TRUE)
ENDIF()

# FOR OTHER OPERATING SYSTEMS
IF(LINUX OR WIN32 OR APPLE)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/android-ifaddrs.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/pthread-fixes.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/aix.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/sunos.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/freebsd.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/netbsd.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/openbsd.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/cygwin.c)

	LIST(REMOVE_ITEM BII_LIB_SRC include/uv-bsd.h)
	LIST(REMOVE_ITEM BII_LIB_SRC include/uv-bsd.h)
	LIST(REMOVE_ITEM BII_LIB_SRC include/uv-sunos.h)
ENDIF()

# OSX SPECIFIC FILES
IF(NOT APPLE)
	LIST(REMOVE_ITEM BII_LIB_SRC include/uv-darwin.h)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/darwin.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/darwin-proctitle.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/fsevents.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/kqueue.c)
	LIST(REMOVE_ITEM BII_LIB_SRC src/unix/kqueue.c)
ENDIF()

# WINDOWS SPECIFIC FILES
IF(NOT WIN32)
	FOREACH(loop_var ${BII_LIB_SRC})
		if(loop_var MATCHES "src/win/(.*)")
	      LIST(REMOVE_ITEM BII_LIB_SRC ${loop_var})
	    endif()
	ENDFOREACH()
	LIST(REMOVE_ITEM BII_LIB_SRC test/runner-win.c)
ENDIF()


# LINUX SPECIFIC FILES
IF(NOT LINUX)
      LIST(REMOVE_ITEM BII_LIB_SRC src/unix/linux-core.c)
      LIST(REMOVE_ITEM BII_LIB_SRC src/unix/linux-inotify.c)
      LIST(REMOVE_ITEM BII_LIB_SRC src/unix/linux-syscalls.c)
      LIST(REMOVE_ITEM BII_LIB_SRC src/unix/linux-syscalls.h)
ENDIF()


# WINDOWS
IF(WIN32)
	FOREACH(loop_var ${BII_LIB_SRC})
		if(loop_var MATCHES "src/unix/(.*)")
	      LIST(REMOVE_ITEM BII_LIB_SRC ${loop_var})
	    endif()
		if(loop_var MATCHES "samples/(.*)")
	      LIST(REMOVE_ITEM BII_LIB_SRC ${loop_var})
	    endif()

	ENDFOREACH()
	LIST(REMOVE_ITEM BII_LIB_SRC test/runner-unix.c)
	IF(";${BII_BLOCK_EXES};" MATCHES ";samples_socks5-proxy_main;")
		LIST(REMOVE_ITEM BII_BLOCK_EXES "samples_socks5-proxy_main") # Not compile in v0.11
	endif()
ENDIF()

#MESSAGE(ERROR ${BII_LIB_SRC})

# Actually create targets: EXEcutables and libraries.
ADD_BIICODE_TARGETS()

IF(LINUX)
    MESSAGE("Compiling for LINUX")
	# TARGET_COMPILE_OPTIONS(${BII_LIB_TARGET} PUBLIC -g -pedantic -Wall -Wextra -Wno-unused-parameter)
	TARGET_LINK_LIBRARIES(${BII_LIB_TARGET} PUBLIC m)
	TARGET_LINK_LIBRARIES(${BII_LIB_TARGET} PUBLIC dl)
	TARGET_LINK_LIBRARIES(${BII_LIB_TARGET} PUBLIC rt)
	SET_TARGET_PROPERTIES(${BII_LIB_TARGET} PROPERTIES COMPILE_DEFINITIONS "IOV_MAX=255") # FIXME its suppose to be in limits.h
ELSEIF(WIN32)
	TARGET_LINK_LIBRARIES(${BII_LIB_TARGET} PUBLIC ws2_32.lib psapi.lib iphlpapi.lib shell32.lib)
	TARGET_COMPILE_OPTIONS(${BII_LIB_TARGET} PUBLIC -DWIN32_LEAN_AND_MEAN)
	IF(MINGW)
		TARGET_LINK_LIBRARIES(${BII_LIB_TARGET} PUBLIC m)
		TARGET_COMPILE_OPTIONS(${BII_LIB_TARGET} PUBLIC -D_WIN32_WINNT=0x0600)
	ELSE() # VISUAL
		TARGET_COMPILE_OPTIONS(${BII_LIB_TARGET} PUBLIC -D_WIN32_WINNT=0x0600)
		TARGET_COMPILE_OPTIONS(${BII_LIB_TARGET} PUBLIC /MTd)
	ENDIF()
ELSEIF(APPLE)
	ADD_OSX_FRAMEWORK(Foundation ${BII_LIB_TARGET})
	ADD_OSX_FRAMEWORK(CoreServices ${BII_LIB_TARGET})
	ADD_OSX_FRAMEWORK(ApplicationServices ${BII_LIB_TARGET})
	TARGET_COMPILE_OPTIONS(${BII_LIB_TARGET} PUBLIC -D_DARWIN_USE_64_BIT_INODE=1 -D_DARWIN_UNLIMITED_SELECT=1)
	IF(CMAKE_CXX_COMPILER MATCHES ".*clang")
		TARGET_COMPILE_OPTIONS(${BII_LIB_TARGET} PUBLIC -Wno-dollar-in-identifier-extension)
	ENDIF()
ENDIF()

TARGET_INCLUDE_DIRECTORIES(${BII_LIB_TARGET} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src)
TARGET_INCLUDE_DIRECTORIES(${BII_LIB_TARGET} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
TARGET_INCLUDE_DIRECTORIES(${BII_LIB_TARGET} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include/uv-private)