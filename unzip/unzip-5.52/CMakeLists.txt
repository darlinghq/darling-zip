project(unzip)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DTARGET_OS_MAC=1)
add_definitions(-D__APPLE__ -D__DYNAMIC__)
add_definitions(-DUNIX -DUNICODE_SUPPORT -DCRYPT -DUSE_ZLIB)

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
	envargs.c
	explode.c
	extract.c
	globals.c
	inflate.c
	list.c
	match.c
	process.c
	timezone.c
	ttyio.c
	unreduce.c
	unshrink.c
	unix/unix.c
)
add_library(unzip_common OBJECT ${common_sources})

set(unzip_sources
	unzip.c
	fileio.c
	crctab.c
	$<TARGET_OBJECTS:unzip_common>
)

add_darling_executable(unzip ${unzip_sources})
target_link_libraries(unzip z)

set(zipinfo_sources
	zipinfo.c
	fileio.c
	unzip.c
	$<TARGET_OBJECTS:unzip_common>
)

add_darling_executable(zipinfo ${zipinfo_sources})
target_link_libraries(zipinfo z)

set(funzip_sources
	funzip.c
	crc32.c
	crypt.c
	globals.c
	inflate.c
	ttyio.c
)

add_darling_executable(funzip ${funzip_sources})
target_link_libraries(funzip z)
set_target_properties(funzip PROPERTIES COMPILE_DEFINITIONS "FUNZIP")

add_darling_executable(unzipsfx ${unzip_sources})
target_link_libraries(unzipsfx z)
set_target_properties(unzipsfx PROPERTIES COMPILE_DEFINITIONS "SFX")

install(TARGETS unzip zipinfo funzip unzipsfx
	DESTINATION libexec/darling/usr/bin)
install(PROGRAMS unix/zipgrep
	DESTINATION libexec/darling/usr/bin)

