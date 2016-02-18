project(zip)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DTARGET_OS_MAC=1)
add_definitions(-D__APPLE__ -D__DYNAMIC__)
add_definitions(-DUSE_ZLIB -DUNIX -DUNICODE_SUPPORT -DCRYPT)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -U_POSIX_C_SOURCE -D__DARWIN_UNIX03 -nostdinc -fblocks -fPIC -ggdb -O0")
set(CMAKE_EXE_LINKER_FLAGS "-nodefaultlibs -nostdlib -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")
#set(CMAKE_SHARED_LINKER_FLAGS "-nodefaultlibs -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/malloc)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/FreeBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/NetBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/gen)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/darwin)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/stdtime/FreeBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/launchd/liblaunch)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/libdispatch)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/zlib)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/bzip2)
include_directories(${DARLING_TOP_DIRECTORY}/src/ncurses/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

include(InstallSymlink)
set(BINARY_PACKAGING_MODE ON)

set(common_sources
	crc32.c
	crypt.c
	deflate.c
	fileio.c
	globals.c
	timezone.c
	trees.c
	ttyio.c
	util.c
	zbz2err.c
)
add_library(zip_common OBJECT ${common_sources})

set(zip_sources
	zip.c
	$<TARGET_OBJECTS:zip_common>
)

add_darling_executable(zip ${zip_sources})
target_link_libraries(zip z)

set(zipcloak_sources
	zipcloak.c
	$<TARGET_OBJECTS:zip_common>
)

add_darling_executable(zipcloak ${zipcloak_sources})
target_link_libraries(zipcloak z)

set(zipnote_sources
	zipnote.c
	$<TARGET_OBJECTS:zip_common>
)

add_darling_executable(zipnote ${zipcloak_sources})
target_link_libraries(zipnote z)

set(zipsplit_sources
	zipsplit.c
	$<TARGET_OBJECTS:zip_common>
)

add_darling_executable(zipsplit ${zipsplit_sources})
target_link_libraries(zipsplit z)


install(TARGETS zip zipcloak zipnote zipsplit
	DESTINATION libexec/darling/usr/bin)

